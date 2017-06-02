---
title: "搭配 HDInsight 來使用 Ambari Tez 檢視 | Microsoft Docs"
description: "了解如何在 HDInsight 上使用 Ambari Tez 檢視來為 Tez 作業偵錯。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 4dcccb313c517982680266656c36b9f23dee44c4
ms.contentlocale: zh-tw
ms.lasthandoff: 05/18/2017


---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>在 HDInsight 上使用 Ambari 檢視來為 Tez 作業偵錯

適用於 HDInsight 的 Ambari Web UI 包含 Tez 檢視，可用來了解使用 Tez 的作業，和為該類型作業偵錯。 Tez 檢視可讓您把作業視覺化有已連接項目的圖表、深入每個項目、取得統計資料，以及記錄資訊。

> [!IMPORTANT]
> 本文件中的步驟需要一個使用 Linux 的 HDInsight 叢集。 Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 元件版本設定](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date)。

## <a name="prerequisites"></a>必要條件

* 以 Linux 為基礎的 HDInsight 叢集。 如需建立叢集的步驟，請參閱[開始使用以 Linux 為基礎的 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)。
* 支援 HTML5 的新式網頁瀏覽器。

## <a name="understanding-tez"></a>了解 Tez

Tez 是 Hadoop 中資料處理用的可延伸架構，資料處理的速度比傳統的 MapReduce 處理方式還要快。 而對於 Linux 型 HDInsight 叢集來說，Tez 是 Hive 的預設引擎。

Tez 會建立有向非循環圖 (DAG)，說明作業所需動作的順序。 個別的動作稱為頂點，它會執行整體作業的一部分。 而由端點所描述的實際工作 (Work) 執行程序稱為工作 (Task)，且可能會分散到叢集中的多個節點上。

### <a name="understanding-the-tez-view"></a>了解 Tez 檢視

Tez 檢視同時提供歷程記錄資訊和正在執行之處理序的資訊。 這項資訊顯示作業如何分散到叢集。 它也會顯示工作和頂點使用的計數器，以及與作業相關的錯誤資訊。 它可能會提供下列案例中的有用資訊：

* 監視長期執行的處理序、檢視對應進度，以及減少工作。
* 分析成功或失敗的處理序歷史資料，以便了解如何改進處理序，或是處理序失敗的原因。

## <a name="generate-a-dag"></a>產生 DAG

Tez 檢視只有在使用 Tez 引擎的作業目前正在執行，或先前曾執行過才會包含資料。 簡單的 Hive 查詢可不使用 Tez 進行解析。 複雜的查詢會執行諸如篩選、群組、排序、聯結等等 則需使用 Tez 引擎。

使用下列步驟來執行使用 Tez 的 Hive 查詢：

1. 在網頁瀏覽器中瀏覽至 https://CLUSTERNAME.azurehdinsight.net，其中 **CLUSTERNAME** 是 HDInsight 叢集的名稱。

2. 在頁面頂端的功能表中，選取 [檢視] 圖示。 此圖示看起來像一系列正方形。 在隨後出現的下拉式清單中，選取 [Hive 檢視]。

    ![選取 [Hive 檢視]](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. 當 Hive 檢視載入之後，在查詢編輯器中貼上下列查詢，然後按一下 [執行]。

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    當作業完成之後，您應該會在 [查詢處理程序結果] 區段看到輸出。 結果應該會與下列文字類似：

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. 選取 [記錄] 索引標籤。 您會看到類似下列文字的資訊：

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    請儲存 **App id** 的值，因為您會在下一節中使用它。

## <a name="use-the-tez-view"></a>使用 Tez 檢視

1. 在頁面頂端的功能表中，選取 [檢視] 圖示。 在隨後出現的下拉式清單中，選取 [Tez 檢視]。

    ![選取 [Tez 檢視]](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. 當 Tez 檢視載入時，您會看到正在或曾經在叢集上執行的 DAG 清單。

    ![所有 DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. 如果您只有一個項目，則它是您在上一節中執行的查詢。 如果您看到多筆項目，可以在 [應用程式識別碼] 欄位輸入應用程式識別碼，然後按下 Enter 鍵。

4. 選取 [Dag 名稱]。 關於 DAG 的詳細資訊隨即顯示。 您也可以下載包含此資訊之 JSON 檔案的壓縮檔。

    ![DAG 詳細資料](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. [DAG 詳細資料] 上方有幾個連結可用來顯示 DAG 的相關資訊。

   * **DAG 計數器**：顯示此 DAG 的計數器資訊。
   * **圖形檢視**：顯示此 DAG 的圖形表示。
   * **所有頂點**：顯示此 DAG 中的頂點清單。
   * **所有工作**：顯示此 DAG 中所有頂點的工作清單。
   * **所有工作嘗試**：顯示嘗試對此 DAG 執行工作的相關資訊。

     > [!NOTE]
     > 如果您捲動 [頂點]、[工作] 和 [工作嘗試] 的資料行顯示，請注意有連結可以檢視每個資料列的**計數器**和**檢視或下載記錄檔**。

     如果作業執行失敗，[DAG 詳細資料] 會顯示 [FAILED (失敗)] 狀態，以及可前往失敗工作相關資訊的連結。 DAG 詳細資料下方會顯示診斷資訊。

     ![詳述某個失敗作業的 [DAG 詳細資料] 畫面](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

6. 選取 [圖形檢視]。 這個檢視會以圖形化的方式來顯示 DAG。 您可以將滑鼠游標移動到檢視中的每個頂點上，來顯示該頂點的相關資訊。

    ![圖形檢視](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

7. 選取頂點以載入該項目的 [頂點詳細資料]。 選取 [對應 1] 頂點來顯示此項目的詳細資料。

    ![頂點詳細資料](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

8. 現在頁面頂端有與頂點和工作相關的連結。

   > [!NOTE]
   > 您也可以回到 [DAG 詳細資料]、選取 [頂點詳細資料]，然後選取 [對應 1] 頂點，就可進入此頁面。

   * **頂點計數器**：顯示此頂點的計數器資訊。
   * **工作**：顯示此頂點的工作。
   * **工作嘗試**：顯示嘗試對此頂點執行工作的相關資訊。
   * **來源與接收**：顯示此頂點的資料來源和接收。

     > [!NOTE]
     > 跟前一個功能表一樣，您可以捲動 [工作]、[工作嘗試] 及 [來源與接收] 的資料行顯示，來顯示可前往每個項目詳細資訊的連結。

9. 選取 [工作]，然後選取名為 **00_000000** 的項目。 這會顯示這項工作的 [工作詳細資料]。 在此畫面中，您可以檢視 [工作計數器] 和 [工作嘗試]。

   ![作業詳細資料](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

## <a name="next-steps"></a>後續步驟

既然您已了解如何使用 Tez 檢視，請深入了解 [在 HDInsight 上使用 Hive](hdinsight-use-hive.md)。

如需 Tez 的詳細技術資訊，請參閱 [Hortonworks 的 Tez 頁面](http://hortonworks.com/hadoop/tez/)。

如需如何搭配 HDInsight 來使用 Ambari 的詳細資訊，請參閱 [使用 Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)

