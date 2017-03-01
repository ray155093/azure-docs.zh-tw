---
title: "適用於 HDInsight R 伺服器的 Azure 儲存體選項 | Microsoft Docs"
description: "了解 HDInsight 中 R 伺服器之使用者可用的不同儲存體選項"
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2df17cddf629cb72b7fa4d590dfaa69311c96aa4
ms.openlocfilehash: 3e47a7e0382009a07b885a28c6e8d90f9bff9cfb
ms.lasthandoff: 01/10/2017


---
# <a name="azure-storage-options-for-r-server-on-hdinsight"></a>適用於 HDInsight R 伺服器的 Azure 儲存體選項
HDInsight 中的 Microsoft R 伺服器具有 Azure Blob 和 [Azure Data Lake 儲存體](https://azure.microsoft.com/services/data-lake-store/)的存取權，可做為保存資料、程式碼、分析的結果物件等等的方法。

當您在 HDInsight 中建立 Hadoop 叢集時，您可以指定一個 Azure 儲存體帳戶或一個 Data Lake Store。 該帳戶的特定儲存體容器會保留您建立之叢集的檔案系統 (例如，Hadoop 分散式檔案系統)。 基於效能目的，系統會在與您指定的主要儲存體帳戶相同資料中心內建立 HDInsight 叢集。 如需詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md "搭配 HDInsight 使用 Azure Blob 儲存體")。   

## <a name="use-multiple-azure-blob-storage-accounts"></a>使用多個 Azure Blob 儲存體帳戶
如有需要，您可以使用 HDI 叢集存取多個 Azure 儲存體帳戶或容器。 若要這樣做，您必須在建立叢集時於 UI 中指定其他儲存體帳戶，然後遵循下列步驟，以便在 R 中使用它們。  

1. 使用儲存體帳戶名稱 **storage1** 和預設容器 **container1** 建立 HDInsight 叢集。
2. 指定其他儲存體帳戶 **storage2**。  
3. 將 mycsv.csv 檔案複製到 /share 目錄，並對該檔案執行分析。  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. 在 R 程式碼中，將名稱節點設為 **default** ，並設定要處理的目錄和檔案。  

        myNameNode <- "default"
        myPort <- 0

    資料的位置：  

        bigDataDirRoot <- "/share"  

    定義 Spark 計算內容：

        mySparkCluster <- RxSpark(consoleOutput=TRUE)

    設定計算內容：

        rxSetComputeContext(mySparkCluster)

    定義 Hadoop 分散式檔案系統 (HDFS) 檔案系統︰

        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    指定要在 HDFS 中分析的輸入檔案：

        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

所有目錄和檔案的參考會指向儲存體帳戶 wasbs://container1@storage1.blob.core.windows.net。 這是與 HDInsight 叢集關聯的「預設儲存體帳戶」。

現在，假設您想要處理名稱為 mySpecial.csv 的檔案，其所在位置為 **storage2** 中 **container2** 的 /private 目錄。

在 R 程式碼中，將名稱節點參考指向 **storage2** 儲存體帳戶。

````
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0
````

資料的位置：

````
bigDataDirRoot <- "/private"
````

定義 Spark 計算內容：

````
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)
````

設定計算內容：

````
rxSetComputeContext(mySparkCluster)
````

定義 HDFS 檔案系統：

````
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)
````

指定要在 HDFS 中分析的輸入檔案：

````
inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
````

現在，所有目錄和檔案的參考會指向儲存體帳戶 wasbs://container2@storage2.blob.core.windows.net。 這是您已指定的「名稱節點」。

請注意，您必須在 **storage2** 上設定 /user/RevoShare/<SSH username> 目錄，如下所示︰

````
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
````

## <a name="use-an-azure-data-lake-store"></a>使用 Azure Data Lake Store
若要以 HDInsight 帳戶使用 Data Lake Store，您必須對叢集提供您想要使用的每個 Azure Data Lake Store 的存取權。 在 R 指令碼中使用 Data Lake Store 的方式，和使用次要儲存體帳戶的方式 (如先前程序所述) 很像。

## <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>為叢集新增 Azure Data Lake Store 的存取權
您可以使用與 HDInsight 叢集相關聯的 Azure Active Directory (Azure AD) 服務主體來存取 Data Lake Store。

### <a name="to-add-a-service-principal"></a>新增服務主體
1.在建立 HDInsight 叢集時，於 [資料來源] 索引標籤中選取 [叢集 AAD 身分識別]。

2.在 [叢集 AAD 身分識別] 對話方塊的 [選取 AD 服務主體] 底下，選取 [新建]。

在為服務主體命名並建立密碼後，按一下 [管理 ADLS 存取]，以將該服務主體與 Data Lake Store 產生關聯。

若要在建立叢集之後新增一或多個 Data Lake Store 的叢集存取，您可以開啟 Data Lake Store 的 Azure 入口網站執行個體，然後移至 [資料總管] > [存取] > [新增]。 

如需 HDI 叢集新增 Data Lake Store 存取權的相關資訊，請參閱[使用 Azure 入口網站建立 HDInsight 叢集與 Data Lake Store (英文)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#create-an-hdinsight-cluster-with-access-to-azure-data-lake-store) 文章

## <a name="use-the-data-lake-store-with-r-server"></a>搭配 R 伺服器使用 Data Lake Store
一旦您獲得 Data Lake Store 的存取權，就可以在 HDInsight 上的 R 伺服器中使用 Data Lake Store，其方式就和使用次要 Azure 儲存體帳戶一樣。 唯一的差別在於前置詞 **wasb://** 會變更為 **adl://**，如下所示：

````
# Point to the ADL store (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# Create factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# Define the data source
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

以下命令是用來設定 Data Lake 儲存體帳戶與 RevoShare 目錄，並新增來自上述範例的範例 .csv 檔案：

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## <a name="use-azure-files-on-the-edge-node"></a>使用邊緣節點上的 Azure 檔案
另外還有可在邊緣節點上使用的便利資料儲存選項，我們稱之為 [Azure 檔案](../storage/storage-how-to-use-files-linux.md "Azure 檔案")。 它可讓您將 Azure 儲存體的檔案共用掛接至 Linux 檔案系統。 這對於儲存資料檔案、R 指令碼以及結果物件相當便利，該結果物件在稍後可以於邊緣節點 (而不是 HDFS) 上使用原生檔案系統時需要。

Azure 檔案的主要優點是，只要是擁有受支援作業系統 (例如 Windows 或 Linux) 的系統，就可以掛接和使用檔案共用。 例如，您本人或您的小組成員所擁有的另一個 HDInsight 叢集，或是 Azure VM 甚或內部部署系統均可使用 Azure 檔案。

## <a name="next-steps"></a>後續步驟
既然已經了解 Azure 儲存體選項，就可以使用下列連結來探索在 HDInsight 中使用 R 伺服器的其他方式。

* [HDInsight 中的 R 伺服器概觀](hdinsight-hadoop-r-server-overview.md)
* [開始使用 Hadoop 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)
* [將 RStudio Server 新增至 HDInsight (若未在建立叢集期間新增)](hdinsight-hadoop-r-server-install-r-studio.md)
* [適用於 HDInsight 中 R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)


