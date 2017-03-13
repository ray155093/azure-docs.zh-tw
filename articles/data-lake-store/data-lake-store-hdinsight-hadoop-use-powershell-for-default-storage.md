---
title: "PowerShell：Azure HDInsight 叢集搭配 Data Lake Store 做為預設儲存體 | Microsoft Docs"
description: "使用 Azure PowerShell 建立和使用 HDInsight 叢集與 Azure Data Lake"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 01c75f20909c7334981bf407e775656476972276
ms.lasthandoff: 03/03/2017


---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-default-storage"></a>使用 Azure PowerShell 建立搭配 Data Lake Store 的 HDInsight 叢集 (做為預設儲存體)
> [!div class="op_single_selector"]
> * [使用入口網站](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell (針對預設儲存體)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell (針對額外儲存體)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何使用 Azure PowerShell 將搭配 Azure Data Lake Store 的 HDInsight 叢集設定為**預設儲存體**。 如需有關如何建立 HDInsight 叢集搭配 Azure Data Lake Store 做為額外儲存體的指示，請參閱[建立 HDInsight 叢集搭配 Data Lake Store 做為額外儲存體](data-lake-store-hdinsight-hadoop-use-powershell.md)。

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>針對 HDInsight 叢集儲存體使用 Data Lake Store

以下是使用 HDInsight 搭配 Data Lake Store 的一些重要考量：

* HDInsight 3.5 版提供建立可存取 Data Lake Store 做為預設儲存體之 HDInsight 叢集的選項。

* HDInsight Premium 叢集不提供建立可存取 Data Lake Store 做為預設儲存體之 HDInsight 叢集的選項。

* 對於 HBase 叢集 (Windows 和 Linux)，**不支援**使用 Data Lake Store 做為儲存體選項，無論是預設儲存體或額外儲存體。


使用 PowerShell 以設定 HDInsight 來搭配 Data Lake Store 使用，包含下列步驟：

* 建立 Azure Data Lake Store
* 設定 Data Lake Store 以角色為基礎的存取的驗證
* 建立具有 Data Lake Store 驗證的 HDInsight 叢集
* 在叢集上執行測試工作

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更新版本**。 請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* **Windows SDK**。 您可以從[這裡](https://dev.windows.com/en-us/downloads)安裝它。 您使用它來建立安全性憑證。
* **Azure Active Directory 服務主體**。 本教學課程中的步驟提供有關如何在 Azure AD 中建立服務主體的指示。 不過，您必須是 Azure AD 系統管理員，才能建立服務主體。 如果您是 Azure AD 系統管理員，您就可以略過這項先決條件並繼續進行本教學課程。

    **如果您不是 Azure AD 系統管理員**，您將無法執行建立服務主體所需的步驟。 在這樣的情況下，您的 Azure AD 系統管理員必須先建立服務主體，您才能建立搭配 Data Lake Store 的 HDInsight 叢集。 此外，必須使用憑證來建立服務主體，如[使用憑證來建立服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)所述。

## <a name="create-an-azure-data-lake-store"></a>建立 Azure Data Lake Store
依照這些步驟建立 Data Lake Store。

1. 從您的桌面開啟新的 Azure PowerShell 視窗，並輸入下列程式碼片段。 系統提示您登入時，請確定您會使用其中一個訂用帳戶管理員/擁有者身分登入：

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > 如果您在註冊 Data Lake Store 的資源提供者時收到類似 `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` 的錯誤，可能表示您的訂用帳戶不在 Azure Data Lake Store 的允許清單中。 請遵循這些 [指示](data-lake-store-get-started-portal.md)，確保您會啟用 Azure 訂用帳戶來使用 Data Lake Store 公開預覽版。
   >
   >
2. Azure Data Lake Store 帳戶與 Azure 資源群組相關聯。 從建立 Azure 資源群組開始。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![建立 Azure 資源群組](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "建立 Azure 資源群組")
3. 建立 Azure Data Lake Store 帳戶。 您指定的帳戶名稱必須只包含小寫字母和數字。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![建立 Azure Data Lake 帳戶](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "建立 Azure Data Lake 帳戶")
4. 確認已成功建立帳戶。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    此輸出應為 **True**。

5. 使用 Data Lake Store 做為預設儲存體需要您指定根路徑，叢集特定檔案會在叢集建立期間複製到該路徑。 使用下列 Cmdlet 建立根路徑，在下列程式碼片段中為 **/clusters/hdiadlcluster**。

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>設定 Data Lake Store 以角色為基礎的存取的驗證
每一個 Azure 訂用帳戶都與 Azure Active Directory 相關聯。 透過 Azure 傳統入口網站或 Azure Resource Manager API 來存取訂用帳戶資源的使用者與服務，都必須先向 Azure Active Directory 進行驗證。 您可以在 Azure 資源上為 Azure 訂用帳戶和服務指派適當的角色，以授與其存取權限。  對於服務，服務主體會識別 Azure Active Directory (AAD) 中的服務。 本章節將說明如何將 Azure 資源 (您稍早建立的 Azure Data Lake Store 帳戶) 的存取權授與像是 HDInsight 的應用程式服務，方法是建立應用程式的服務主體，並透過 Azure PowerShell 將角色指派給它。

若要設定 Azure Data Lake 的 Active Directory 驗證，您必須執行下列工作。

* 建立自我簽署憑證
* 在 Azure Active Directory 和服務主體中建立應用程式

### <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證
進行本節中的步驟之前，請確定您已安裝 [Windows SDK](https://dev.windows.com/en-us/downloads)。 您也必須建立一個目錄 (例如 **C:\mycertdir**)，以在其中建立憑證。

1. 在 PowerShell 視窗中，瀏覽至您安裝 Windows SDK 的位置 (通常是 `C:\Program Files (x86)\Windows Kits\10\bin\x86`)，並使用 [MakeCert][makecert] 公用程式來建立自我簽署的憑證和私密金鑰。 使用下列命令。

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        
        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    系統會提示您輸入私密金鑰密碼。 命令成功執行之後，您應該會在您指定的憑證目錄中看到 **CertFile.cer** 和 **mykey.pvk**。
2. 使用 [Pvk2Pfx][pvk2pfx] 公用程式將 MakeCert 建立的 .pvk 和 .cer 檔案轉換成 .pfx 檔案。 執行下列命令。

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    系統提示時，輸入您稍早指定的私密金鑰密碼。 您針對 **-po** 參數指定的值是與 .pfx 檔案相關聯的密碼。 命令成功完成之後，您應該也會在您指定的憑證目錄中看到 CertFile.pfx。

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>建立 Azure Active Directory 和服務主體
在這一節中，您將執行相關步驟來建立 Azure Active Directory 應用程式的服務主體、指派角色給服務主體，並藉由提供憑證驗證為服務主體。 執行下列命令以在 Azure Active Directory 中建立應用程式。

1. 在 PowerShell 主控台視窗中貼上下列 Cmdlet。 請確定您針對 **-DisplayName** 屬性指定的值是唯一的。 此外，**-HomePage** 和 **-IdentiferUris** 的值是預留位置值而不會受到驗證。

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. 使用應用程式識別碼建立服務主體。

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. 將服務主體存取權授與 Data Lake Store 根目錄，以及您稍早指定之根路徑中的所有資料夾。 使用下列 Cmdlet。

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-default-storage"></a>建立 HDInsight Linux 叢集搭配 Data Lake Store 做為預設儲存體

在本節中，我們會建立一個 HDInsight Hadoop Linux 叢集搭配 Data Lake Store 做為預設儲存體。 對於此版本，HDInsight 叢集和 Data Lake Store 必須位於相同位置。

1. 開始擷取訂用帳戶租用戶識別碼。 稍後您將會需要此資訊。

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
        
2. 建立 HDInsight 叢集。 使用下列 Cmdlet。

        # Set these variables
        
        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Cmdlet 成功完成後，您應該會看到列出叢集詳細資料的輸出。

        
## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>在 HDInsight 叢集上執行測試工作以使用 Data Lake Store
設定 HDInsight 叢集之後，您可以在叢集上執行測試工作，以測試 HDInsight 叢集是否可以存取 Data Lake Store。 若要這麼做，我們將執行範例 Hive 作業，該作業會使用已在 Data Lake Store 中於 **<cluster root>/example/data/sample.log**提供使用的範例資料建立資料表。

在這一節中，您將透過 SSH 連線到您所建立的 HDInsight Linux 叢集並執行範例 Hive 查詢。

* 如果您使用 Windows 用戶端來透過 SSH 連線到叢集，請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。
* 如果您使用 Linux 用戶端來透過 SSH 連線到叢集，請參閱[從 Linux 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. 連線之後，使用下列命令來啟動 Hive CLI：

        hive
2. 使用 CLI，輸入下列陳述式，以使用 Data Lake Store 中的範例資料來建立名為 **vehicles** 的新資料表：

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    您應該會在 SSH 主控台上看到查詢輸出。 

    > [!NOTE]
       > 上述 CREATE TABLE 命令中範例資料的路徑為 `adl:///example/data/`，其中 `adl:///` 是叢集根目錄。 以本教學課程中所指定之叢集根目錄的範例而言，這會是 `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`。 因此，您可以使用較短的替代路徑，或提供叢集根目錄的完整路徑。 
       >
       >



## <a name="access-data-lake-store-using-hdfs-commands"></a>使用 HDFS 命令存取 Data Lake Store
一旦您已設定 HDInsight 叢集使用 Data Lake Store，您可以使用 HDFS 殼層命令來存取存放區。

在這一節中，您將透過 SSH 連線到您所建立的 HDInsight Linux 叢集並執行 HDFS 命令。 

* 如果您使用 Windows 用戶端來透過 SSH 連線到叢集，請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。
* 如果您使用 Linux 用戶端來透過 SSH 連線到叢集，請參閱[從 Linux 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

連接之後，使用下列 HDFS 檔案系統命令列出 Data Lake Store 中的檔案。

    hdfs dfs -ls adl:///

您也可以使用 `hdfs dfs -put` 命令來將一些檔案上傳至 Data Lake Store，然後使用 `hdfs dfs -ls` 以確認是否成功上傳檔案。

## <a name="see-also"></a>另請參閱
* [入口網站：建立 HDInsight 叢集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

