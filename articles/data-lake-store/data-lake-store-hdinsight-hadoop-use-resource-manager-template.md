---
title: "使用 Resource Manager 範本來建立搭配 Azure Data Lake Store 的 HDInsight 叢集 | Microsoft Docs"
description: "使用 Azure Resource Manager 範本來建立和使用搭配 Azure Data Lake Store 的 HDInsight 叢集"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 3f8c9b22fb9a7aae97c43e39fe82a610f6b8b374
ms.openlocfilehash: 2d3415c654307b50420abe1e7e61938c91088552


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本來建立搭配 Data Lake Store 的 HDInsight 叢集
> [!div class="op_single_selector"]
> * [使用入口網站](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>

了解如何使用 Azure Resource Manager 範本來設定可存取 Azure Data Lake Store 的 HDInsight 叢集。 Data Lake Store 對於支援的叢集類型，是做為預設儲存體或額外儲存體帳戶。 當 Data Lake Store 做為額外儲存體時，叢集的預設儲存體帳戶仍然是 Azure 儲存體 Blob (WASB)，叢集相關的檔案 (例如記錄檔等) 仍然會寫入預設儲存體，而您想要處理的資料會儲存於 Data Lake Store 帳戶中。 使用 Data Lake Store 做為其他儲存體帳戶，不會影響效能或從叢集讀取/寫入至儲存體的能力。

一些重要考量︰

* HDInsight 3.5 版提供建立可存取 Data Lake Store 做為預設儲存體之 HDInsight 叢集的選項。

* HDInsight 3.2、3.4 和 3.5 版提供建立可存取 Data Lake Store 做為額外儲存體之 HDInsight 叢集的選項。

* 對於 HBase 叢集 (Windows 和 Linux)，**不支援**使用 Data Lake Store 做為儲存體選項，無論是預設儲存體或額外儲存體。


在本文中，我們佈建 Hadoop 叢集與資料湖存放區做為額外的儲存體。 如需有關如何建立以 Data Lake Store 做為預設儲存體之 Hadoop 叢集的指示，請參閱[使用 Azure 入口網站建立搭配 Data Lake Store 的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更新版本**。 請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。
* **Azure Active Directory 服務主體**。 本教學課程中的步驟提供有關如何在 Azure AD 中建立服務主體的指示。 不過，您必須是 Azure AD 系統管理員，才能建立服務主體。 如果您是 Azure AD 系統管理員，您就可以略過這項先決條件並繼續進行本教學課程。

    **如果您不是 Azure AD 系統管理員**，您將無法執行建立服務主體所需的步驟。 在這樣的情況下，您的 Azure AD 系統管理員必須先建立服務主體，您才能建立搭配 Data Lake Store 的 HDInsight 叢集。 此外，必須使用憑證來建立服務主體，如[使用憑證來建立服務主體](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)所述。

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>建立搭配 Azure Data Lake Store 的 HDInsight 叢集
GitHub 的[使用新的 Data Lake Store 來部署 HDInsight Linux 叢集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)提供了 Resource Manager 範本，以及使用該範本的先決條件。 請依照此連結所提供的指示來建立以 Azure Data Lake Store 作為額外儲存體的 HDInsight 叢集。

上述連結所提供的指示需要使用 PowerShell。 從這些指示開始著手之前，請確定您已登入 Azure 帳戶。 從您的桌面開啟新的 Azure PowerShell 視窗，然後輸入下列程式碼片段。 系統提示您登入時，請確定您會使用其中一個訂用帳戶管理員/擁有者身分登入：

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>將範例資料上傳到 Azure Data Lake Store
Resource Manager 範本會建立一個新的 Data Lake Store 帳戶並將它與 HDInsight 叢集建立關聯。 您現在必須將一些範例資料上傳到 Data Lake Store。 稍後在教學課程中，您將需要這項資料，以從會存取資料湖存放區中資料的 HDInsight 叢集中執行作業。 如需有關如何上傳資料的指示，請參閱[將檔案上傳至 Data Lake Store](data-lake-store-get-started-portal.md#uploaddata)。 如果您正在尋找一些可上傳的範例資料，可以從 **Azure 資料湖 Git 存放庫** 取得 [Ambulance Data](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)資料夾。

## <a name="set-relevant-acls-on-the-sample-data"></a>在範例資料上設定相關的 ACL
為了確定可從 HDInsight 叢集存取您上傳的範例資料，您必須確保用來在 HDInsight 叢集與 Data Lake Store 之間建立身分識別的 Azure AD 應用程式能夠存取您嘗試存取的檔案/資料夾。 若要這樣做，請執行下列步驟。

1. 找出與 HDInsight 叢集和 Data Lake Store 關聯的 Azure AD 應用程式名稱。 其中一個尋找名稱的方法是開啟您使用 Resource Manager 範本來建立的 HDInsight 叢集刀鋒視窗、按一下 [叢集 AAD 識別] 索引標籤，然後尋找 [服務主體顯示名稱] 的值。
2. 現在，將您想要從 HDInsight 叢集存取之檔案/資料夾的存取權提供給此 Azure AD 應用程式。 若要在 Data Lake Store 中的檔案/資料夾上設定正確的 ACL，請參閱[保護 Data Lake Store 中的資料](data-lake-store-secure-data.md#filepermissions)。

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>在 HDInsight 叢集上執行測試工作以使用資料湖存放區
設定 HDInsight 叢集之後，您可以在叢集上執行測試工作，以測試 HDInsight 叢集是否可以存取資料湖存放區。 為了完成這個操作，我們將會執行範例 Hive 工作，該工作會使用您稍早上傳至資料湖存放區的範例資料建立資料表。

### <a name="for-a-linux-cluster"></a>對於 Linux 叢集
在這一節中，您將 SSH 進入叢集並執行範例 Hive 查詢。 Windows 未提供內建 SSH 用戶端。 建議使用 **PuTTY**，您可以從下列位置下載： [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

如需使用 PuTTY 的詳細資訊，請參閱 [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

1. 連線之後，使用下列命令來啟動 Hive CLI：

   ```
   hive
   ```
2. 使用 CLI，輸入下列陳述式，以使用 Data Lake Store 中的範例資料來建立名為 **vehicles** 的新資料表：

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   您應該會看到如下所示的輸出：

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```

### <a name="for-a-windows-cluster"></a>對於 Windows 叢集
使用下列 Cmdlet 以執行 Hive 查詢。 在這個查詢中，我們將會從資料湖存放區中的資料建立資料表，然後在建立的資料表上執行 select 查詢。

```
$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials
```

輸出如下。 **ExitValue** 為 0 表示工作成功完成。

```
Cluster         : hdiadlcluster.
HttpEndpoint    : hdiadlcluster.azurehdinsight.net
State           : SUCCEEDED
JobId           : job_1445386885331_0012
ParentId        :
PercentComplete :
ExitValue       : 0
User            : admin
Callback        :
Completed       : done
```

使用下列 Cmdlet 從工作擷取輸出：

```
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials
```

工作輸出類似下面：

```
1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
```

## <a name="access-data-lake-store-using-hdfs-commands"></a>使用 HDFS 命令存取資料湖存放區
一旦您已設定 HDInsight 叢集使用資料湖存放區，您可以使用 HDFS 殼層命令來存取存放區。

### <a name="for-a-linux-cluster"></a>對於 Linux 叢集
在這一節中，您將 SSH 進入叢集並執行 HDFS 命令。 Windows 未提供內建 SSH 用戶端。 建議使用 **PuTTY**，您可以從下列位置下載： [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

如需使用 PuTTY 的詳細資訊，請參閱 [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

連接之後，使用下列 HDFS 檔案系統命令列出資料湖存放區中的檔案。

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

這樣應該會列出您稍早上傳至資料湖存放區的檔案。

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

您也可以使用 `hdfs dfs -put` 命令來將一些檔案上傳至資料湖存放區，然後使用 `hdfs dfs -ls` 以確認是否成功上傳檔案。

### <a name="for-a-windows-cluster"></a>對於 Windows 叢集
1. 登入新的 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [瀏覽]、按一下 [HDInsight 叢集]，然後按一下您建立的 HDInsight 叢集。
3. 在 [叢集] 刀鋒視窗中，按一下 [遠端桌面]，然後在 [遠端桌面] 刀鋒視窗中，按一下 [連接]。

   ![遠端至 HDI 叢集](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png)

   出現提示時，請輸入為遠端桌面使用者提供的認證。
4. 在遠端工作階段中，啟動 Windows PowerShell，並使用 HDFS 檔案系統命令來列出 Azure 資料湖存放區中的檔案。

   ```
   hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
   ```

   這樣應該會列出您稍早上傳至資料湖存放區的檔案。

   ```
   15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
   Found 1 items
   -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv
   ```

   您也可以使用 `hdfs dfs -put` 命令來將一些檔案上傳至資料湖存放區，然後使用 `hdfs dfs -ls` 以確認是否成功上傳檔案。

## <a name="next-steps"></a>後續步驟
* [將資料從 Azure 儲存體 Blob 複製到 Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)



<!--HONumber=Nov16_HO4-->


