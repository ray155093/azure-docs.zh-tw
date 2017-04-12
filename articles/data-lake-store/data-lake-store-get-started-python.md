---
title: "透過 Python SDK 開始使用 Azure Data Lake Store | Microsoft Docs"
description: "了解如何利用 Python SDK 來與 Data Lake Store 帳戶及檔案系統搭配使用。"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 9e4efc9de7979c98fcb4afbe530c73e9013326c3
ms.lasthandoff: 04/06/2017


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>透過 Python 開始使用 Azure Data Lake Store

> [!div class="op_single_selector"]
> * [入口網站](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用適用於 Azure 和 Azure Data Lake Store 的 Python SDK 來執行基本作業，例如建立資料夾、上傳和下載資料檔案等等。如需有關 Data Lake 的詳細資訊，請參閱 [Azure Data Lake Store](data-lake-store-overview.md)。

## <a name="prerequisites"></a>必要條件

* **Python**。 您可以從[這裡](https://www.python.org/downloads/)下載 Python。 本文章使用 Python 3.5.2。

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **建立 Azure Active Directory 應用程式**。 您必須使用 Azure AD 應用程式來向 Azure AD 驗證 Data Lake Store 應用程式。 有不同的方法可向 Azure AD 進行驗證：**使用者驗證**或**服務對服務驗證**。 如需如何驗證的指示和詳細資訊，請參閱 [使用 Azure Active Directory 向 Data Lake Store 進行驗證](data-lake-store-authenticate-using-active-directory.md)。

## <a name="install-the-modules"></a>安裝模組

若要透過 Python 使用 Data Lake Store，您需要安裝三個模組。

* `azure-mgmt-resource` 模組。 這包括適用於 Active Directory 等等的 Azure 模組。
* `azure-mgmt-datalake-store` 模組。 這包括 Azure Data Lake Store 帳戶管理作業。 如需關於此模組的詳細資訊，請參閱 [Azure Data Lake Store Management module reference (Azure Data Lake Store 管理模組參考)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)。
* `azure-datalake-store` 模組。 這包括 Azure Data Lake Store 檔案系統作業。 如需關於此模組的詳細資訊，請參閱 [Azure Data Lake Store Filesystem module reference (Azure Data Lake Store 檔案系統模組參考)](http://azure-datalake-store.readthedocs.io/en/latest/)。

使用下列命令來安裝新模組。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

1. 在您選定的整合式開發環境 (IDE) 中建立新的 Python 應用程式，例如 **mysample.py**。

2. 加入以下文字行以匯入必要模組

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. 將變更儲存至 mysample.py。

## <a name="authentication"></a>驗證

在本節中，我們會討論向 Azure AD 進行驗證的各種方式。 可用的選項如下︰

* 使用者驗證
* 服務對服務驗證
* Multi-Factor Authentication

您必須對帳戶管理和檔案系統管理模組使用這些驗證選項。

### <a name="end-user-authentication-for-account-management"></a>適用於帳戶管理的使用者驗證

使用這個方法來向 Azure AD 驗證，以便進行帳戶管理作業 (建立/刪除 Data Lake Store 帳戶等等)。 您必須提供 Azure AD 使用者的使用者名稱和密碼。 請注意，使用者不應設定使用多重要素驗證。

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>檔案系統作業的使用者驗證

使用這個方法來向 Azure AD 驗證，以便進行檔案系統作業 (建立資料夾、上傳檔案等等)。 請將此方法用於現有的 Azure AD「原生用戶端」應用程式。 您提供認證的 Azure AD 使用者不應設定使用多重要素驗證。

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>適用於帳戶管理的用戶端密碼服務對服務驗證

使用這個方法來向 Azure AD 驗證，以便進行帳戶管理作業 (建立/刪除 Data Lake Store 帳戶等等)。 下列程式碼片段可供使用應用程式/服務主體的用戶端密碼，以非互動方式驗證您的應用程式。 請將此方法用於現有的 Azure AD「Web 應用程式」應用程式。

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>適用於檔案系統作業的用戶端密碼服務對服務驗證

使用這個方法來向 Azure AD 驗證，以便進行檔案系統作業 (建立資料夾、上傳檔案等等)。 下列程式碼片段可供使用應用程式/服務主體的用戶端密碼，以非互動方式驗證您的應用程式。 請將此方法用於現有的 Azure AD「Web 應用程式」應用程式。

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

### <a name="multi-factor-authentication-for-account-management"></a>適用於帳戶管理的 Multi-Factor Authentication

使用這個方法來向 Azure AD 驗證，以便進行帳戶管理作業 (建立/刪除 Data Lake Store 帳戶等等)。 下列程式碼片段可供使用 Multi-Factor Authentication 來驗證您的應用程式。 請將此方法用於現有的 Azure AD「Web 應用程式」應用程式。

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="multi-factor-authentication-for-filesystem-management"></a>適用於檔案系統管理的 Multi-Factor Authentication

使用這個方法來向 Azure AD 驗證，以便進行檔案系統作業 (建立資料夾、上傳檔案等等)。 下列程式碼片段可供使用 Multi-Factor Authentication 來驗證您的應用程式。 請將此方法用於現有的 Azure AD「Web 應用程式」應用程式。

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>建立 Azure 資源群組

使用下列程式碼片段來建立 Azure 資源群組︰

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-clients-and-data-lake-store-account"></a>建立用戶端與 Data Lake Store 帳戶

以下程式碼片段會先建立 Data Lake Store 帳戶用戶端。 它會使用用戶端物件來建立 Data Lake Store 帳戶。 最後，程式碼片段會建立檔案系統用戶端物件。

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>列出 Data Lake Store 帳戶

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="create-a-directory"></a>建立目錄

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>上傳檔案


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>下載檔案

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>刪除目錄

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="see-also"></a>另請參閱

- [保護 Data Lake Store 中的資料](data-lake-store-secure-data.md)
- [搭配 Data Lake Store 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [搭配 Data Lake Store 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Store .NET SDK 參考](https://msdn.microsoft.com/library/mt581387.aspx)
- [Data Lake Store REST 參考](https://msdn.microsoft.com/library/mt693424.aspx)

