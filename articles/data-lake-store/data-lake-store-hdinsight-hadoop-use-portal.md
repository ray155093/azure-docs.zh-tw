---
title: "使用 Azure 入口網站建立搭配 Data Lake Store 運作的 HDInsight Hadoop 叢集 | Microsoft Docs"
description: "使用 Azure 入口網站建立和使用搭配 Azure Data Lake Store 運作的 HDInsight Hadoop 叢集"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f7c977dc2e385819dada976afa9497e9a20fd90c
ms.lasthandoff: 03/22/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>使用 Azure 入口網站建立搭配 Data Lake Store 的 HDInsight 叢集
> [!div class="op_single_selector"]
> * [使用 Azure 入口網站](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell (針對預設儲存體)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell (針對額外儲存體)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

本文討論如何使用 Azure 入口網站建立可存取 Azure Data Lake Store 的 HDInsight 叢集。 對於支援的叢集類型，您可以使用 Data Lake Store 做為預設儲存體或額外儲存體帳戶。 

當您使用 Data Lake Store 做為額外儲存體時，叢集的預設儲存體帳戶仍然是 Azure Blob 儲存體，而叢集相關的檔案 (例如記錄) 仍會寫入預設儲存體。 不過，您想要處理的資料可以儲存於 Data Lake Store 帳戶中。 使用 Data Lake Store 做為額外儲存體帳戶，並不會影響效能或從叢集讀取或寫入至儲存體的能力。

以下是使用 HDInsight 搭配 Data Lake Store 的一些重要考量：

* HDInsight 3.5 版提供建立可存取 Data Lake Store 做為預設儲存體之 HDInsight 叢集的選項。

* HDInsight Premium 叢集「不提供」建立可存取 Data Lake Store 做為預設儲存體之 HDInsight 叢集的選項。

* HDInsight 3.2、3.4 和 3.5 版提供建立可存取 Data Lake Store 做為額外儲存體之 HDInsight 叢集的選項。

* 對於 HBase 叢集 (Windows 和 Linux)，「不支援」使用 Data Lake Store 做為儲存體選項，無論是預設或額外儲存體。

* 對於 Storm 叢集 (Windows 和 Linux)，您可以使用 Data Lake Store 從 Storm 拓撲寫入資料。 您也可以使用 Data Lake Store 做為參考資料，該資料稍後可以由 Storm 拓撲讀取。 如需詳細資訊，請參閱 [在 Storm 拓撲中使用 Data Lake Store](#use-data-lake-store-in-a-storm-topology)。


## <a name="prerequisites"></a>必要條件
開始本教學課程之前，確保您已符合下列需求：

* **Azure 訂用帳戶**。 請移至[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帳戶**。 遵循[使用 Azure 入口網站開始使用 Azure Data Lake Store](data-lake-store-get-started-portal.md) 的指示操作。

* **Azure Active Directory 服務主體**。 本教學課程提供有關如何在 Azure Active Directory (Azure AD) 中建立服務主體的指示。 不過，您必須是 Azure AD 系統管理員，才能建立服務主體。 如果您是系統管理員，就可以略過這項先決條件並繼續進行本教學課程。

    >[!NOTE]
    >唯有您是 Azure AD 系統管理員，才可以建立服務主體。 您的 Azure AD 系統管理員必須先建立服務主體，您才能建立搭配 Data Lake Store 的 HDInsight 叢集。 此外，必須使用憑證來建立服務主體，如[使用憑證來建立服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)所述。
    >

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>建立可存取 Data Lake Store 的 HDInsight 叢集
在本節中，您會建立使用 Data Lake Store 做為預設儲存體或額外儲存體的 HDInsight Hadoop 叢集。

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>建立以 Data Lake Store 做為預設儲存體的叢集

>[!NOTE]
>此選項只適用於 HDInsight 3.5 叢集 (Standard Edition)。 在 HDInsight 3.5 叢集中，此選項不適用於 HBase 叢集類型。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 若要開始佈建 HDInsight 叢集，請依照[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)中的指示操作。

3. 在 [儲存體] 刀鋒視窗的 [主要儲存體類型] 之下，按一下 [Data Lake Store]。

4. 選取現有的 Data Lake Store 帳戶，並輸入將儲存叢集特定檔案的根資料夾路徑。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "將服務主體新增至 HDInsight 叢集")

    
    在上方的螢幕擷取畫面中，根資料夾路徑是 /clusters/myhdiadlcluster，其中 *myhdiadlcluster* 是要建立的叢集名稱。 在此情況下，請確定 Data Lake Store 帳戶中存在 /cluster 資料夾。 建立叢集時，會建立 *myhdiadlcluster* 資料夾。 同樣地，如果根路徑設為 /hdinsight/clusters/data/myhdiadlcluster，確保 Data Lake Store 帳戶中存在 /hdinsight/clusters/data/。

5. 按一下 [Data Lake Store 存取] 以設定 Data Lake Store 帳戶和 HDInsight 叢集之間的存取。 如需指示，請參閱[設定 HDInsight 叢集和 Data Lake Store 之間的存取](#configure-access-between-hdinsight-cluster-and-data-lake-store)。 


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>建立以 Data Lake Store 做為額外儲存體的叢集 

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 若要開始佈建 HDInsight 叢集，請依照[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)中的指示操作。

3. 在 [儲存體] 刀鋒視窗的 [主要儲存體類型] 之下，選取 [Azure 儲存體]。
 
4. 在 [選取方法] 之下，執行下列其中一個動作：

    * 若要指定屬於 Azure 訂用帳戶的儲存體帳戶，請選取 [我的訂用帳戶]，然後選取儲存體帳戶。

    * 若要指定 Azure 訂用帳戶外部的儲存體帳戶，請選取 [存取金鑰]，然後提供外部儲存體帳戶資訊。

5. 在 [預設容器] 之下，保留入口網站所建議的預設容器名稱，或是自行指定名稱。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "將服務主體新增至 HDInsight 叢集")

6. 當您使用 Blob 儲存體做為預設儲存體時，您仍然可以使用 Data Lake Store 做為叢集的額外儲存體。 若要這麼做，請按一下 [Data Lake Store 存取] 以設定 Data Lake Store 帳戶和 HDInsight 叢集之間的存取。 如需指示，請參閱[設定 HDInsight 叢集和 Data Lake Store 之間的存取](#configure-access-between-hdinsight-cluster-and-data-lake-store)。

## <a name="configure-access-between-hdinsight-cluster-and-data-lake-store"></a>設定 HDInsight 叢集和 Data Lake Store 之間的存取

在本節中，您將會使用 Azure Active Directory 服務主體設定 HDInsight 叢集和 Data Lake Store 之間的存取

1. 在 [Data Lake Store 存取] 刀鋒視窗上，指定您想要使用新的服務主體或是現有的服務主體。 此步驟會建立新的服務主體。 若要使用現有的服務主體，請跳至下一個步驟。

    a. 在 [Data Lake Store 存取權] 刀鋒視窗，按一下 [新建]，然後按一下 [服務主體]。

    b. 在 [建立服務主體]刀鋒視窗，輸入必要的值。  

    c. 按一下 [建立] 。 系統會自動建立憑證和 Azure AD 應用程式。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "將服務主體新增至 HDInsight 叢集")

    您也可以按一下 [下載憑證]，以下載與您所建立服務主體關聯的憑證。 如果您想要在建立其他 HDInsight 叢集時使用相同的服務主體，下載憑證非常實用。 按一下 [選取] 。

2. 若要使用現有的服務主體，請執行下方提供的步驟。

    a. 在 [Data Lake Store 存取權] 刀鋒視窗，按一下 [使用現有的]，然後按一下 [服務主體]。

    b. 在 [選取服務主體] 刀鋒視窗中，搜尋現有的服務主體。 按一下您想使用的服務主體，然後按一下 [選取]。

    c. 在 [Data Lake Store 存取權] 刀鋒視窗中，上傳與您所選取服務主體相關聯的憑證 (.pfx 檔案)，然後輸入憑證密碼。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "將服務主體新增至 HDInsight 叢集")

7. 在 [Data Lake Store 存取權] 刀鋒視窗中，按一下 [存取]。 預設會開啟 [選取檔案權限] 刀鋒視窗。 其中列出您訂用帳戶中的所有 Data Lake Store 帳戶。

8. 選取您想要與叢集相關聯的 Data Lake Store 帳戶。 

    「如果您使用 Data Lake Store 做為預設儲存體」，您應該指派兩個層級的權限。

    a. 首先，您應該於 Data Lake Store 帳戶的根層級指派權限。 若要這麼做，請將滑鼠游標暫留 (請勿按一下) 在 Data Lake Store 帳戶的名稱上，以顯示核取方塊。 選取核取方塊。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "將服務主體新增至 HDInsight 叢集")

    b. 然後，您應該將權限指派至 HDInsight 叢集儲存體根。 根據先前的螢幕擷取畫面，叢集儲存體根為您在選取 Data Lake Store 做為預設儲存體時所指定的 **/clusters** 資料夾。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.add.png "將服務主體新增至 HDInsight 叢集")

    「如果您使用 Data Lake Store 做為額外儲存體」，您必須僅針對您想要從 HDInsight 叢集存取的資料夾指派權限。 例如，在下方的螢幕擷取畫面中，您僅會將存取提供給 Data Lake Store 帳戶中的 **hdiaddonstorage** 資料夾。

    ![將服務主體權限指派給 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "將服務主體權限指派給 HDInsight 叢集")

9. 針對所有選取的帳戶和路徑，請選取權限 (讀取、寫入或執行)，並指定權限是否同時要以遞迴方式套用到子項目，然後按一下 [選取]。

11. 在 [指派選取的權限] 視窗中，若要針對 Azure Active Directory 服務主體指派所選帳戶、檔案、資料夾的權限，請按一下 [執行]。

    ![將服務主體權限指派給 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "將服務主體權限指派給 HDInsight 叢集")

12. 順利指派權限之後，請在每個開啟的刀鋒視窗上按一下 [完成]，以返回 [Data Lake Store 存取權] 刀鋒視窗。

13. 在 [Data Lake Store 存取權] 上按一下 [選取]，然後繼續進行叢集建立作業，如[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)所述。

## <a name="verify-cluster-set-up"></a>確認叢集設定

叢集設定完成之後，在叢集刀鋒視窗上執行下列任何一個動作來驗證結果︰

* 若要驗證叢集的相關儲存體是否為您指定的 Data Lake Store 帳戶，請按一下左窗格中的 [儲存體帳戶]。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "將服務主體新增至 HDInsight 叢集")

* 若要驗證服務主體是否正確地與 HDInsight 叢集相關聯，請按一下左窗格中的 [Data Lake Store 存取權]。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "將服務主體新增至 HDInsight 叢集")


## <a name="examples"></a>範例

在您設定以 Data Lake Store 做為儲存體的叢集之後，請參考一些範例以了解如何使用 HDInsight 叢集來分析 Data Lake Store 中儲存的資料。

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>針對 Data Lake Store (做為主要儲存體) 中的資料執行 Hive 查詢

若要執行 Hive 查詢，請使用 Ambari 入口網站中的 Hive 檢視介面。 如需有關如何使用 Ambari Hive 檢視的指示，請參閱[在 HDInsight 中搭配 Hadoop 使用 Hive 檢視](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md)。

當您使用 Data Lake Store 中的資料時，有幾個字串需要變更。

如果您使用以 Data Lake Store 做為主要儲存體而建立的叢集，則資料路徑為︰adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file。 從 Data Lake Store 帳戶中儲存的範例資料建立資料表的 Hive 查詢類似於︰

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

說明：
* `adl://hdiadlstorage.azuredatalakestore.net/` 是 Data Lake Store 帳戶的根。
* `/clusters/myhdiadlcluster` 是您在建立叢集時指定之叢集資料的根目錄。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` 是您在查詢中使用之範例檔案的位置。

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>針對 Data Lake Store (做為額外儲存體) 中的資料執行 Hive 查詢

如果您建立的叢集使用 Blob 儲存體做為預設儲存體，則範例資料不會包含在做為額外儲存體的 Azure Data Lake Store 帳戶中。 在這種情況下，請先將資料從 Blob 儲存體傳送至 Data Lake Store，然後如上述範例所示執行查詢。

如需如何將資料從 Blob 儲存體複製到 Data Lake Store 的詳細資訊，請參閱下列文章：

* [使用 Distcp 在 Azure 儲存體 Blob 與 Data Lake Store 之間複製資料](data-lake-store-copy-data-wasb-distcp.md)
* [使用 AdlCopy 將資料從 Azure 儲存體 Blob 複製到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>使用 Data Lake Store 搭配 Spark 叢集
您可以使用 Spark 叢集對 Data Lake Store 中儲存的資料執行 Spark 作業。 如需詳細資訊，請參閱[使用 HDInsight Spark 叢集來分析 Data Lake Store 中的資料](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md)。


### <a name="use-data-lake-store-in-a-storm-topology"></a>在 Storm 拓撲中使用 Data Lake Store
您可以使用 Data Lake Store 從 Storm 拓撲寫入資料。 如需有關如何達到這種情況的指示，請參閱 [搭配使用 Azure Data Lake Store 與 HDInsight 上的 Apache Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md)。

## <a name="see-also"></a>另請參閱
* [PowerShell：建立 HDInsight 叢集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

