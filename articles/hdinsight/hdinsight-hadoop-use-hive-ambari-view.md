---
title: "使用 Ambari 檢視與 HDInsight (Hadoop) 上的 Hive 搭配作業 - Azure | Microsoft Docs"
description: "了解如何從網頁瀏覽器使用 Hive 檢視來提交 Hive 查詢。 Hive 檢視是以 Linux 為基礎的 HDInsight 叢集隨附的 Ambari 檢視的一部分。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/05/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 9adfd92b72eb0ecd5b4811c525d4b6ff6609f60f
ms.contentlocale: zh-tw
ms.lasthandoff: 07/08/2017


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>在 HDInsight 中搭配 Hadoop 使用 Hive 檢視

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

了解如何使用 Ambari Hive 檢視執行 Hive 查詢。 Ambari 是隨著以 Linux 為基礎的 HDInsight 叢集提供的管理和監視公用程式。 透過 Ambari 提供的功能之一是可以用來執行 Hive 查詢的 Web UI。

> [!NOTE]
> Ambari 有許多本文未討論到的功能。 如需詳細資訊，請參閱 [使用 Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a name="prerequisites"></a>必要條件

* 以 Linux 為基礎的 HDInsight 叢集。 如需建立叢集的相關資訊，請參閱[開始使用以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)。

> [!IMPORTANT]
> 此文件中的步驟需要使用 Linux 的 HDInsight 叢集。 Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="open-the-hive-view"></a>開啟 Hive 檢視

您可以從 Azure 入口網站存取 Ambari 檢視，選取您的 HDInsight 叢集，然後從 [快速連結] 區段選取 [Ambari 檢視]。

![快速連結區段](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

您也可以在網頁瀏覽器中移至 https://CLUSTERNAME.azurehdinsight.net，來直接瀏覽至 Ambari。 將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。 若要列出可用的檢視，請從功能表中選取方形集合。 若要開啟檢視，請選取 [Hive 檢視] 項目。

![選取 Ambari 檢視](./media/hdinsight-hadoop-use-hive-ambari-view/select-hive-view.png)。

> [!NOTE]
> 存取 Ambari 時，系統會提示您對網站進行驗證。 輸入您在建立叢集時所使用的管理 (預設為 `admin`) 帳戶名稱和密碼。

您應該會看到類似下圖的頁面：

![Hive 檢視頁面的影像，包含查詢編輯器區段](./media/hdinsight-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="view-tables"></a>檢視資料表

在頁面的 [資料庫總管] 區段上，選取 [資料庫] 索引標籤上的 [預設] 項目。 預設資料庫中的資料表清單隨即出現。 HDInsight 包含名為 **hivesampletable** 的資料表。

![已展開預設資料庫的資料庫總管](./media/hdinsight-hadoop-use-hive-ambari-view/database-explorer.png)

透過這份文件中的步驟新增資料表時，您可以使用 [資料庫總管] 右上角的重新整理圖示來重新整理清單。

## <a name="hivequery"></a>查詢編輯器

若要執行 Hive 查詢，請從 Hive 檢視使用下列步驟。

1. 在頁面的 [查詢編輯器] 區段中，將下列 HiveQL 陳述式貼到工作表中：

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    這些陳述式會執行下列動作：

   * `DROP TABLE` - 刪除資料表和資料檔 (如果資料表已存在)。

   * `CREATE EXTERNAL TABLE` - 在 Hive 中建立新的「外部」資料表。
   外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。

   * `ROW FORMAT` - 設定資料格式的方式。 在此情況下，每個記錄中的欄位會以空格隔開。

   * `STORED AS TEXTFILE LOCATION` - 資料的儲存位置，且資料會儲存為文字。

   * `SELECT` - 選取在資料行 t4 中包含 [ERROR] 值的所有資料列。

     > [!NOTE]
     > 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，自動化的資料上傳程序，或透過其他 MapReduce 作業。 捨棄外部資料表並 *不會* 刪除資料，只會刪除資料表定義。

2. 若要開始查詢，請使用 [查詢編輯器] 底部的 [執行] 按鈕。 它會變成橘色，而且文字會變更為**停止執行**。 [查詢程序結果] 區段應該會出現在 [查詢編輯器] 下方並顯示作業相關資訊。

   > [!IMPORTANT]
   > 有些瀏覽器可能無法正確地重新整理記錄檔或結果資訊。 如果您執行一項作業，而該作業似乎會一直執行，但未更新記錄檔或傳回結果，請嘗試改用 Mozilla FireFox 或 Google Chrome。

3. 查詢完成後，[查詢程序結果] 區段會顯示作業的結果。 查詢完成時，[停止執行] 按鈕也會變回綠色 [執行] 按鈕。 [結果]  索引標籤應包含下列資訊：

        sev       cnt
        [ERROR]   3

    [記錄檔] 索引標籤可用來檢視作業所建立的記錄資訊。

   > [!TIP]
   > [查詢程序結果] 區段左上角的 [儲存結果] 下拉式對話方塊可讓您下載或儲存結果。

4. 選取此查詢的前四行，然後選取 [執行]。 請注意，作業完成時沒有任何結果。 在選取部分查詢的情況下使用 [執行] 按鈕，只會執行所選的陳述式。 在此情況下，選取項目不包含從資料表擷取資料列的最後一個陳述式。 如果您只選取那一行並使用 [執行]，您應該會看到預期的結果。

5. 若要新增工作表，請使用 [查詢編輯器] 底部的 [新增工作表] 按鈕。 在新的工作表中，輸入下列 HiveQL 陳述式：

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  這些陳述式會執行下列動作：

   * **CREATE TABLE IF NOT EXISTS** - 建立資料表 (如果不存在)。 由於未使用  關鍵字，因此會建立內部資料表。 內部資料表儲存在 Hive 資料倉儲中，並完全受到 Hive 所管理。 與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

   * **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 ORC 是高度最佳化且有效率的 Hive 資料儲存格式。

   * **INSERT OVERWRITE ...SELECT** - 從包含 `[ERROR]` 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。

     使用 [查詢] 按鈕執行此查詢。 當查詢傳回零個資料列時，[結果] 索引標籤不會包含任何資訊。 查詢完成後，狀態應該會顯示為 [成功]。

### <a name="hive-settings"></a>Hive 設定

選取編輯器右側的 [設定] 圖示。

![Hive 檢視的設定圖示](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-settings-icon.png)

設定可用來變更各種 Hive 設定。 比方說，將 Hive 的執行引擎從 Tez (預設值) 變更為 MapReduce。

### <a name="visualization"></a>視覺效果

選取編輯器右側的 [視覺效果] 圖示。

![Hive 檢視的視覺效果圖示](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization-icon.png)

您可以在視覺效果介面中為查詢所傳回的資料建立視覺效果。 下圖是使用 `hivesampletable` (隨附於 HDInsight) 的資料所建立的範例視覺效果：

![範例視覺效果](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization.png)

### <a name="visual-explain"></a>視覺解說

選取編輯器右側的 [視覺解說] 圖示。

![Hive 檢視的視覺解說圖示](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visual-explain-icon.png)

查詢的 [視覺解說] 檢視有助於了解複雜查詢的流程。 您可以使用 [查詢編輯器] 中的 [解說] 按鈕來檢視此檢視的對等文字。

![視覺解說影像](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez

選取編輯器右側的 [Tez] 圖示。

![Hive 檢視的 Tez 圖示](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-tez-icon.png)

如果使用 Tez 來解析查詢，則會顯示有向非循環圖 (DAG)。 如果您想要檢視您已執行過之查詢的 DAG，或偵錯 Tez 程序，請改用 [Tez 檢視](hdinsight-debug-ambari-tez-view.md) 。

### <a name="notifications"></a>通知

選取編輯器右側的 [通知] 圖示。

![Hive 檢視的通知圖示](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-notifications-icon.png)

通知是在執行查詢時產生的訊息。 例如，您會在提交查詢時或發生錯誤時收到通知。

## <a name="saved-queries"></a>儲存的查詢

1. 從 [查詢編輯器] 建立工作表，並輸入下列查詢：

    ```hiveql
    SELECT * from errorLogs;
    ```

    執行查詢以驗證它是否能正常運作。 結果類似於下列範例：

        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id

2. 使用編輯器底部的 [另存新檔] 按鈕。 將此查詢命名為 **Errorlogs** ，然後選取 [確定]。 工作表的名稱會變更為 **Errorlogs**。

3. 選取 [Hive 檢視] 頁面頂端的 [儲存的查詢] 索引標籤。 **Errorlogs** 現在會列為儲存的查詢。 它會保留在此清單中，直到您移除它。 選取名稱會在 [查詢編輯器] 中開啟查詢。

## <a name="query-history"></a>查詢歷程記錄

[Hive 檢視] 頂端的 [記錄] 按鈕可讓您檢視先前執行的查詢。 現在使用它並且選取您先前執行的其中一個查詢。 當您選取查詢時，它會在 [查詢編輯器] 中開啟。

## <a name="user-defined-functions-udf"></a>使用者定義函數 (UDF)

Hive 也可透過 **使用者定義函數 (UDF)**延伸。 UDF 可讓您在 HiveQL 中實作功能或不易模型化的邏輯。

[Hive 檢視] 頂端的 [UDF] 索引標籤可讓您宣告並儲存一組 UDF。 這些 UDF 可以在 [查詢編輯器] 中使用。

一旦您將 UDF 新增至 [Hive 檢視]，[插入 udf] 按鈕隨即會出現在 [查詢編輯器] 底端。 選取這個項目會顯示 [Hive 檢視] 中定義之 UDF 的下拉式清單。 選取 UDF 會將 HiveQL 陳述式新增至查詢以啟用 UDF。

例如，如果您使用下列屬性定義 UDF：

* 資源名稱：myudfs

* 資源路徑︰/myudfs.jar

* UDF 名稱：myawesomeudf

* UDF 類別名稱：com.myudfs.Awesome

使用 [插入 udf] 按鈕會顯示名為 [myudfs] 的項目，以及針對該資源定義的每個 UDF 的另一個下拉式清單。 在此案例中為 **myawesomeudf**。 選取此項目會將下列項目新增至查詢的開頭：

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

您接著可以在查詢中使用 UDF。 例如， `SELECT myawesomeudf(name) FROM people;`。

如需在 HDInsight 上搭配 Hive 使用 UDF 的詳細資訊，請參閱下列文件：

* [在 HDInsight 中搭配 Hive 與 Pig 使用 Python](hdinsight-python.md)
* [如何將自訂 Hive UDF 新增至 HDInsight (英文)](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a id="nextsteps"></a>接續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)
* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

