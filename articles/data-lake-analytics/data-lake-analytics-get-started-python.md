---
title: "透過 Python 開始使用 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何使用 Python 來建立 Data Lake Store 帳戶及提交作業。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>教學課程：透過 Python 開始使用 Azure Data Lake Analytics
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何透過 Python 建立 Azure Data Lake Analytics 帳戶、在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定義 Data Lake Analytics 作業，以及將作業提交至 Data Lake Analytics 帳戶。 如需有關資料湖分析的詳細資訊，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

在本教學課程中，您會開發一個作業以讀取定位鍵分隔值 (TSV) 檔案，並將該檔案轉換為逗點分隔值 (CSV) 檔案。 若要使用其他支援的工具進行同一個教學課程，請按一下此區段最上方的索引標籤。

##<a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列項目：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure Active Directory 應用程式**。 您必須使用 Azure AD 應用程式來向 Azure AD 驗證 Data Lake Store 應用程式。 有不同的方法可向 Azure AD 進行驗證：使用者驗證或服務對服務驗證。 如需如何驗證的指示和詳細資訊，請參閱 [使用 Azure Active Directory 向 Data Lake Store 進行驗證](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。
- **[Python](https://www.python.org/downloads/)**。 您必須使用 64 位元版本。 本文章使用 Python 3.5.2 版。


## <a name="install-azure-python-sdk"></a>安裝 Azure Python SDK

若要透過 Python 使用 Data Lake Store，您需要安裝三個模組。

azure-mgmt-datalake-store 模組包括 Azure Data Lake Store 帳戶管理作業。 azure-mgmt-resource 模組包括適用於 Active Directory 等等的其他 Azure 模組。azure-datalake-store 模組包含 Azure Data Lake Store 檔案系統作業。 azure-datalake-analytics 模組包含 Azure Data Lake Analytics 作業。 使用下列命令來安裝新模組。

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>建立 Python 應用程式

1. 使用選定 IDE 來建立新的 Python 應用程式，如 mysample.py。

2. 加入以下文字行以匯入必要模組

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store.account import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.account.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. 儲存 Python 應用程式檔案的變更。

## <a name="authentication"></a>驗證

使用下列其中一種方法來驗證：

### <a name="end-user-authentication-for-account-management"></a>適用於帳戶管理的使用者驗證

使用這個方法來向 Azure AD 驗證，以便進行帳戶管理作業 (建立/刪除 Data Lake Store 帳戶等)。 您必須提供 Azure AD 使用者的使用者名稱和密碼。 使用者帳戶不可設定為使用多重要素驗證，且帳戶不能是 Microsoft 帳戶 / Live ID (如 @outlook.com, 和 @live.com.)

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>適用於帳戶管理的用戶端密碼服務對服務驗證

使用這個方法來向 Azure AD 驗證，以便進行帳戶管理作業 (建立/刪除 Data Lake Store 帳戶等)。 下列程式碼片段可供使用應用程式/服務主體的用戶端密碼，以非互動方式驗證您的應用程式。 請將此方法用於現有的 Azure AD「Web 應用程式」應用程式。

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>建立資源管理群組

使用下列程式碼片段來建立 Azure 資源群組︰

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>建立 Data Lake Store 帳戶

每個 Data Lake Analytics 帳戶都必須擁有 Data Lake Store 帳戶。 如需指示，請參閱[建立 Data Lake Store 帳戶](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account)。



## <a name="create-data-lake-analytics-account"></a>建立資料湖分析帳戶

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>提交資料湖分析工作

資料湖分析工作是以 U-SQL 語言撰寫。 若要深入了解 U-SQL，請參閱[開始使用 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>後續步驟

- 若要使用其他工具檢視同一個教學課程，請按一下頁面最上方的索引標籤選取器。
- 若要了解更複雜的查詢，請參閱 [使用 Azure 資料湖分析來分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
- 若要開始開發 U-SQL 應用程式，請參閱 [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要了解 U-SQL，請參閱 [開始使用 Azure 資料湖分析 U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
- 針對管理工作，請參閱 [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
- 若要取得資料湖分析概觀，請參閱 [Azure 資料湖分析概觀](data-lake-analytics-overview.md)。




<!--HONumber=Nov16_HO4-->


