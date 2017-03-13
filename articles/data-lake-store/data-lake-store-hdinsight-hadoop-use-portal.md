---
title: "使用網頁瀏覽器建立 Azure HDInsight 與 Data Lake Store | Microsoft Docs"
description: "使用 Azure 入口網站建立和使用 HDInsight Hadoop 叢集與 Azure Data Lake Store"
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
ms.date: 02/16/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 8876a37b78fa8a80eba7af133d661c3d7ed425d7
ms.openlocfilehash: 76e098525951d122799f11bdcd9ee5451c9a3777
ms.lasthandoff: 03/01/2017


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>使用 Azure 入口網站建立 HDInsight 叢集與 Data Lake Store
> [!div class="op_single_selector"]
> * [使用入口網站](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell (針對預設儲存體)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell (針對額外儲存體)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何使用 Azure 入口網站建立可存取 Azure Data Lake Store 的 HDInsight 叢集。 Data Lake Store 對於支援的叢集類型，是做為預設儲存體或額外儲存體帳戶。 當 Data Lake Store 做為額外儲存體時，叢集的預設儲存體帳戶仍然是 Azure 儲存體 Blob (WASB)，叢集相關的檔案 (例如記錄檔等) 仍然會寫入預設儲存體，而您想要處理的資料會儲存於 Data Lake Store 帳戶中。 使用 Data Lake Store 做為其他儲存體帳戶，不會影響效能或從叢集讀取/寫入至儲存體的能力。

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>針對 HDInsight 叢集儲存體使用 Data Lake Store

以下是使用 HDInsight 搭配 Data Lake Store 的一些重要考量：

* HDInsight 3.5 版提供建立可存取 Data Lake Store 做為預設儲存體之 HDInsight 叢集的選項。

* HDInsight 3.2、3.4 和 3.5 版提供建立可存取 Data Lake Store 做為額外儲存體之 HDInsight 叢集的選項。

* 對於 HBase 叢集 (Windows 和 Linux)，**不支援**使用 Data Lake Store 做為儲存體選項，無論是預設儲存體或額外儲存體。

* 對於 Storm 叢集 (Windows 和 Linux)，Data Lake Store 可用來從 Storm 拓撲寫入資料。 Data Lake Store 也可以用來儲存參考資料，該資料稍後可以由 Storm 拓撲讀取。 如需詳細資訊，請參閱 [在 Storm 拓撲中使用 Data Lake Store](#use-data-lake-store-in-a-storm-topology)。


## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帳戶**。 遵循 [使用 Azure 入口網站開始使用 Azure Data Lake Store](data-lake-store-get-started-portal.md) 的指示。

* **Azure Active Directory 服務主體**。 本教學課程中的步驟提供有關如何在 Azure AD 中建立服務主體的指示。 不過，您必須是 Azure AD 系統管理員，才能建立服務主體。 如果您是 Azure AD 系統管理員，您就可以略過這項先決條件並繼續進行本教學課程。

    **如果您不是 Azure AD 系統管理員**，您將無法執行建立服務主體所需的步驟。 在這樣的情況下，您的 Azure AD 系統管理員必須先建立服務主體，您才能建立搭配 Data Lake Store 的 HDInsight 叢集。 此外，必須使用憑證來建立服務主體，如[使用憑證來建立服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)所述。

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>建立可存取 Azure Data Lake Store 的 HDInsight 叢集
在本節中，您會建立 HDInsight Hadoop 叢集，它使用 Data Lake Store 做為額外的儲存體。 在此版本中，對於 Hadoop 叢集，Data Lake Store 只能做為叢集的額外儲存體。 預設儲存體仍是 Azure 儲存體 Blob (WASB)。 所以，我們要先建立叢集所需的儲存體帳戶和儲存體容器。

1. 登入新的 [Azure 入口網站](https://portal.azure.com)。

2. 請依照 [在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-provision-clusters.md) 中的步驟，開始佈建 HDInsight 叢集。

3. 在 [儲存體] 刀鋒視窗中，指定您要 Azure 儲存體 (WASB) 或 Data Lake Store 做為您的預設儲存體。 如果您要使用 Azure Data Lake Store 做為預設儲存體，請跳至下一個步驟。

    如果您想要 Azure 儲存體 Blob 做為預設儲存體，對於 [主要儲存體類型]，請按一下 [Azure 儲存體]。 接下來，針對 [選取方法]，如果您想要指定屬於您 Azure 訂用帳戶一部分的儲存體帳戶，您可以選擇 [我的訂閱]，然後選取該儲存體帳戶。 否則，按一下 [存取金鑰]，然後提供您希望從您的 Azure 訂用帳戶以外選擇之儲存體帳戶的資訊。 針對 [預設容器]，您可以選擇使用入口網站建議的預設容器名稱，或者自行指定名稱。 

    當您使用 Azure 儲存體 Blob 做為預設儲存體時，您仍然可以使用 Azure Data Lake Store 做為叢集的額外儲存體。 若要這樣做，請按一下 [Data Lake Store 存取權]，然後跳至步驟 5。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "將服務主體新增至 HDInsight 叢集")


4. 如果您想要 Azure Data Lake Store 做為預設儲存體，對於 [主要儲存體類型]，請按一下 [Data Lake Store]。 選取已經存在的 Data Lake Store 帳戶，提供將儲存叢集特定檔案的根資料夾路徑，將 [位置] 指定為 [美國東部 2]，然後按一下 [Data Lake Store 存取權]。 此選項只適用於 HDInsight 3.5 叢集 (Standard Edition)。 在 HDInsight 3.5 叢集中，此選項不適用於 HBase 叢集類型。

    在下列螢幕擷取畫面中，根資料夾路徑是 /clusters/myhdiadlcluster，其中 **myhdiadlcluster** 是要建立的叢集名稱。 在此情況下，請確定 Data Lake Store 帳戶中已經有 **/cluster** 資料夾。 建立叢集時，會建立 **myhdiadlcluster** 資料夾。 同樣地，如果根路徑設為 /hdinsight/clusters/data/myhdiadlcluter，必須確定 Data Lake Store 帳戶中已經有 **/hdinsight/clusters/data/**。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "將服務主體新增至 HDInsight 叢集")

5. 在 [Data Lake Store 存取權] 刀鋒視窗中，您可以選擇選取現有的服務主體或建立一個新的服務主體。 如果您想要使用現有的服務主體，請跳至下一個步驟。

    如果您想要建立新的服務主體，請在 [Data Lake Store 存取權] 刀鋒視窗中，按一下 [建立新項目]、按一下 [服務主體]，然後在 [建立服務主體] 刀鋒視窗中，提供值以建立新的服務主體。 在這個過程中，也會建立憑證和 Azure Active Directory 應用程式。 按一下 [建立] 。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "將服務主體新增至 HDInsight 叢集")

    您也可以按一下 [下載憑證]，以下載與您所建立服務主體關聯的憑證。 日後在建立其他 HDInsight 叢集時如果您想要使用相同的服務主體，便很適合這麼做。 按一下 [選取] 。

6. 如果您想要使用現有的服務主體，請在 [Data Lake Store 存取權] 刀鋒視窗中，按一下 [使用現有的項目]、按一下 [服務主體]，然後在 [選取服務主體] 刀鋒視窗中，搜尋現有的服務主體。 按一下服務主體名稱，然後按一下 [選取] 。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "將服務主體新增至 HDInsight 叢集")

    在 [Data Lake Store 存取權] 刀鋒視窗中，上傳與您所選取服務主體相關聯的憑證 (.pfx)，然後提供憑證密碼。

6. 在 [Data Lake Store 存取權] 刀鋒視窗中，按一下 [存取]。 在下一個窗格中，預設已選取 [選取檔案權限]，並列出您訂用帳戶中所有的 Data Lake Store 帳戶。 按一下您想要與叢集相關聯的 Data Lake Store 帳戶，以列出該帳戶中的檔案和資料夾。 然後，您可以指派檔案或資料夾層級的權限。 如果您想要關聯帳戶根層級的權限，請選取帳戶名稱旁邊的核取方塊。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "將服務主體新增至 HDInsight 叢集")

    > [!NOTE]
    > 如果您使用 Data Lake Store 帳戶做為叢集的預設儲存體，您**必須**對 Data Lake Store 帳戶之根層級的的服務主體指派權限。

7. 如果您想要為帳戶內的檔案或資料夾指派權限，請在下一個窗格中選取 Data Lake Store 帳戶以查看檔案/資料夾。 選取檔案/資料夾，選取您想要對其指派的權限 (讀取/寫入/執行)，指定權限是否同時要以遞迴方式套用到子項目，然後按一下 [選取]。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "將服務主體新增至 HDInsight 叢集")

8. 在下一個畫面中，按一下 [執行] 為 Azure Active Directory 服務主體您所選取的帳戶、檔案、資料夾指派權限。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "將服務主體新增至 HDInsight 叢集")

9. 順利指派權限之後，請在所有刀鋒視窗上按一下 [完成]，直到您回到 [Data Lake Store 存取權] 刀鋒視窗。

4. 在 [Data Lake Store 存取權] 上按一下 [選取]，然後繼續進行叢集建立作業，如[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)所述。

10. 佈建叢集之後，您可以確認相關聯的儲存體是否為指定的 Data Lake Store 帳戶。 您可以按一下叢集刀鋒視窗的 [儲存體帳戶] 索引標籤加以確認。 

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "將服務主體新增至 HDInsight 叢集")

    您也可以確認「服務主體」是否與 HDInsight 叢集關聯。 若要這樣做，請從叢集刀鋒視窗中，按一下 [Data Lake Store 存取權] 來查看關聯的「服務主體」。

    ![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "將服務主體新增至 HDInsight 叢集")

## <a name="show-me-some-examples"></a>請舉例說明

在您佈建 Data Lake Store 做為儲存體的叢集之後，以下是一些範例有關如何使用 HDInsight 叢集來分析 Data Lake Store 中儲存的資料。

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store-as-primary-storage"></a>針對 Data Lake Store (做為主要儲存體) 中儲存的資料執行 Hive 查詢

若要執行 Hive 查詢，您可以使用 Ambari 入口網站中的 Hive 檢視介面。 如需有關如何使用 Ambari Hive 檢視的指示，請參閱[在 HDInsight 中搭配 Hadoop 使用 Hive 檢視](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md)。 使用 Data Lake Store 中的資料時，您必須變更幾處。

* 如果您將我們透過 Data Lake Store 建立的叢集範例做為主要儲存體，資料路徑會是 `adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file`。 從 Data Lake Store 帳戶中儲存的範例資料建立資料表的 Hive 查詢會類似︰

        CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

在上述查詢中，

* `adl://hdiadlstorage.azuredatalakestore.net/` 是 Data Lake Store 帳戶的根。
* `/clusters/myhdiadlcluster` 是您在建立叢集時指定之叢集資料的根目錄。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` 是您在查詢中使用的範例檔案的位置

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store-as-additional-storage"></a>針對 Data Lake Store (做為其他儲存體) 中儲存的資料執行 Hive 查詢

如果您所建立的叢集使用 Azure 儲存體 (WASB) 做為預設儲存體，則範例資料不會出現在做為其他儲存體的 Azure Data Lake Store 帳戶中。 在這種情況下，您必須先將資料從 WASB 傳送至 Azure Data Lake Store，然後以上述同樣的方式執行查詢。

如需如何將資料從 WASB 複製到 Azure Data Lake Store 的詳細資訊，請參閱下列連結：

* [使用 Distcp 在 Azure 儲存體 Blob 與 Data Lake Store 之間複製資料](data-lake-store-copy-data-wasb-distcp.md)
* [使用 AdlCopy 將資料從 Azure 儲存體 Blob 複製到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md) 

### <a name="use-data-lake-store-with-spark-cluster"></a>使用 Data Lake Store 搭配 Spark 叢集
您可以使用 Spark 叢集對 Data Lake Store 中儲存的資料執行 Spark 作業。 如需上述的指示，請參閱[使用 HDInsight Spark 叢集來分析 Data Lake Store 中的資料](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md)。


### <a name="use-data-lake-store-in-a-storm-topology"></a>在 Storm 拓撲中使用 Data Lake Store
您可以使用 Data Lake Store 從 Storm 拓撲寫入資料。 如需有關如何達到這種情況的指示，請參閱 [搭配使用 Azure Data Lake Store 與 HDInsight 上的 Apache Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md)。

## <a name="see-also"></a>另請參閱
* [PowerShell：建立 HDInsight 叢集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

