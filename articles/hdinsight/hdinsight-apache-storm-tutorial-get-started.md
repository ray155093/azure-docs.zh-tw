---
title: "Apache Storm 教學課程：開始使用 Storm | Microsoft Docs"
description: "在 HDInsight 上使用 Apache Storm 和 Storm Starter 範例，開始分析巨量資料。 了解如何使用 Storm 即時處理資料。"
keywords: "apache storm,apache storm 教學課程,巨量資料分析,storm 入門"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6f93f7ff-0736-4708-80ef-4289dae532a9
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: e505d02895abd011661b3e4f66c7f4f7ea042358
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>在 HDInsight 上使用 Storm Starter 範例開始分析巨量資料

Apache Storm 是一個可處理資料串流的分散式、容錯、即時的運算系統。 在 Microsoft Azure HDInsight 的 Storm 中，您可以建立雲端式 Storm 叢集，來執行即時的巨量資料分析。 

> [!IMPORTANT]
> 本文中的步驟會建立以 Windows 為基礎的 HDInsight 叢集。 Windows 上的 HDInsight 只提供低於 HDInsight 3.4 的版本。 Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。
>
> 如需在 HDInsight 叢集上建立以 Linux 為基礎之 Storm 的步驟，請參閱 [Apache Storm 教學課程：在 HDInsight 上藉由資料分析開始使用 Storm Starter 範例](hdinsight-apache-storm-tutorial-get-started-linux.md)

## <a name="prerequisites"></a>必要條件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

您必須具備下列先決條件，才能順利完成本 Apache Storm 教學課程：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

### <a name="access-control-requirements"></a>存取控制需求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>建立 Storm 叢集

請使用下列步驟建立 Storm on HDInsight 叢集：

1. 從 [Azure 入口網站](https://portal.azure.com)選取 [+ 新增]、[情報 + 分析] 及 [HDInsight]，然後選取 [HDInsight]。
   
    ![建立 HDInsight 叢集](./media/hdinsight-apache-storm-tutorial-get-started/create-hdinsight.png)

2. 在 [基本概念] 刀鋒視窗中，輸入下列資訊：

    * **叢集名稱**︰HDInsight 叢集的名稱。
    * **訂用帳戶**：選取要使用的訂用帳戶。
    * **叢集登入使用者名稱**和**叢集登入密碼**：透過 HTTPS 存取叢集時使用的登入資訊。 您會使用這些認證來存取例如 Ambari Web UI 或 REST API 等服務。
    * **安全殼層 (SSH) 的使用者名稱**︰將這些欄位保留為預設值。 它們不適用於以 Windows 為基礎的 HDInsight 叢集。
    * **資源群組**：在其中建立叢集的資源群組。
    * **位置**：在其中建立叢集的 Azure 區域。
   
    ![選取訂用帳戶](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-basic-configuration.png)

3. 選取 [叢集類型]，並且在 [叢集組態] 刀鋒視窗中設定下列值︰
   
    * **叢集類型**：Storm

    * **作業系統**：Windows

    * **版本**：Storm 0.10.0 (HDI 3.3)

        > [!NOTE]
        > HDInsight 3.4 版及更新版本僅適用於 Linux 作業系統。

    * **叢集層**：標準
     
    最後，使用 [選取] 按鈕來儲存設定。
     
    ![選取叢集類型](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-cluster-type.png)

4. 選取叢集類型之後，請使用 [選取] 按鈕來設定叢集類型。 接下來，使用 [下一步] 按鈕來完成基本組態。

5. 從 [儲存體] 刀鋒視窗中，選取或建立儲存體帳戶。 本文件的步驟是，將此刀鋒視窗中的其他欄位保留為預設值。 使用 [下一步] 按鈕以儲存儲存體組態。

    ![設定 HDInsight 的儲存體帳戶](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-storage-account.png)

6. 從 [摘要] 刀鋒視窗中，檢閱叢集組態。 使用 [編輯] 連結來變更所有不正確的設定。 最後，使用 [建立] 按鈕來建立叢集。
   
    ![叢集組態摘要](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > 建立叢集可能需要花費 20 分鐘的時間。

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>在 HDInsight 上執行 Storm Starter 範例
本 Apache Storm 教學課程會向您介紹在 GitHub 上使用 Storm Starter 範例的巨量資料分析。

每個 Storm on HDInsight 叢集都會隨附一個 Storm 儀表板，讓您將 Storm 拓撲上傳到叢集並在其中執行 Storm 拓撲。 每個叢集也會隨附可直接從 Storm 儀表板執行的範例拓撲。

### <a id="connect"></a>連接至儀表板
儀表板位於 **https://&lt;clustername>.azurehdinsight.net//**，其中 **clustername** 是叢集的名稱。 您也可以尋找儀表板的連結，方法是從「開始面板」選取叢集，然後在刀鋒視窗頂端選取 [儀表板] 連結。

![含 Storm 儀表板連結的 Azure 入口網站](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [!NOTE]
> 連線至儀表板時，系統會提示您輸入使用者名稱和密碼。 這是您建立叢集時使用的系統管理員名稱 (**admin**) 和密碼。
> 
> 

載入 Storm 儀表板後，您會看到  [提交拓撲] (Submit Topology) 表單。

![利用 Storm 儀表板提交 Storm Starter 拓撲。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

[提交拓撲]  表單可用於上傳並執行含有 Storm 拓撲的 .jar 檔案。 它也包含數個與叢集一併提供的基本範例。

### <a id="run"></a>從 GitHub 的 Storm Starter 專案執行 word-count 範例
與叢集一併提供的範例包含 word-counting 拓撲的數個變體。 這些範例包含會隨機產生句子的 **spout**，以及會將每個句子拆成個別單字，然後計算每個單字的出現次數的 **bolts**。 這些範例均來自 [Storm Starter 範例](https://github.com/apache/storm/tree/master/examples/storm-starter)(Apache Storm 的一部分)。

請使用以下步驟執行 Storm Starter 範例：

1. 選取 [Jar 檔案] 下拉式清單中的 [StormStarter - WordCount]。 此時 [類別名稱] 和 [其他參數] 欄位會填入此範例的參數。
   
    ![在 Storm 儀表板上選取的 Storm Starter WordCount。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)
   
   * **類別名稱** ：提交拓撲的 .jar 檔案中的類別。
   * **其他參數** ：拓撲需要的所有參數。 在此範例中，此欄位用於讓提交的拓撲有易記名稱。
2. 按一下 [提交]。 隨後，[結果]  欄位會顯示用來提交作業的命令，以及該命令的結果。 [錯誤]  欄位會顯示提交拓撲時所發生的任何錯誤。
   
    ![Storm Starter WordCount 的提交按鈕和結果。](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)
   
   > [!NOTE]
   > 顯示結果並不表示拓撲已完成， **Storm 拓撲一旦啟動，就會持續執行，直到您將拓撲停止為止。** Word-count 拓撲會產生隨機的句子，並持續計算每個單字出現的次數，直到您停止拓撲為止。
   > 
   > 

### <a id="monitor"></a>監視拓撲
您可以使用 Storm UI 監視拓撲。

1. 選取 Storm 儀表板頂端的 [Storm UI]  。 這會顯示叢集和所有執行中拓撲的摘要資訊。
   
    ![顯示 Storm Starter WordCount 拓樸摘要的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)
   
    在上方的頁面中，您會看到拓撲作用中的時間，以及使用的背景工作數、執行程式數和工作數。
   
   > [!NOTE]
   > [名稱] 欄含有稍早透過 [其他參數] 欄位提供的易記名稱。
   > 
   > 
2. 在 [拓撲摘要] 下，選取 [名稱] 欄中的 [wordcount] 項目。 這會顯示拓撲的詳細資訊。
   
    ![包含 Storm Starter WordCount 拓樸資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)
   
    此頁面提供以下資訊：
   
   * **拓撲統計資料 (Topology stats)** ：拓撲效能的基本資訊，已整理為時間範圍。
     
     > [!NOTE]
     > 選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。
     > 
     > 
   * **Spouts** ：spout 的基本資訊，包括每個 spout 傳回的最後一個錯誤。
   * **Bolts** ：bolt 的基本資訊。
   * **拓撲組態 (Topology configuration)** ：拓撲組態的詳細資訊。
     
     此頁面也提供可對拓撲採取的動作：
   * **啟用** ：繼續處理已停用的拓撲。
   * **停用** ：暫停執行中拓撲。
   * **重新平衡** ：調整拓撲的平行處理原則。 變更叢集中的節點數目之後，您應該重新平衡執行中拓撲。 這可讓拓撲調整平行處理原則，以彌補叢集中增加/減少的節點數目。 如需詳細資訊，請參閱 [了解 Storm 拓撲的平行處理原則](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。
   * **終止 (Kill)** ：在指定的逾時之後終止 Storm 拓撲。
3. 在此頁面中，選取 [Spouts] 或 [Bolts] 區段中的一個項目。 這會顯示所選元件的相關資訊。
   
    ![包含所選元件相關資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)
   
    此頁面會顯示以下資訊：
   
   * **Spout/Bolt 統計資料 (Spout/Bolt stats)** ：元件效能的基本資訊，已整理為時間範圍。
     
     > [!NOTE]
     > 選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。
     > 
     > 
   * **輸入統計資料 (Input stats)** (僅 bolt)：提供的資訊是關於產生 bolt 所使用之資料的元件。
   * **輸出統計資料 (Output stats)** ：此 bolt 發出之資料的資訊。
   * **執行程式** ：此元件之執行個體的資訊。
   * **錯誤** ：此元件產生的錯誤。
4. 檢視 spout 或 bolt 的詳細資料時，請在 [執行程式] 區段的 [連接埠] 欄中選取一個項目，以檢視特定元件執行個體的詳細資料。
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    您可以在此資料中看到 **seven** 一字已出現 1,493,957 次。 這就是啟動拓撲後，該字所出現的次數。

### <a name="stop-the-topology"></a>停止拓撲
請返回 word-count 拓撲的 [拓撲摘要] 頁面，然後選取 [拓撲動作] 區段中的 [終止] 按鈕。 出現提示時，請先輸入要等候 10 秒，再停止拓撲。 逾時期限過後，當您瀏覽儀表板的 [Storm UI]  區段時，就不會再看到拓撲。

## <a name="delete-the-cluster"></a>刪除叢集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="summary"></a>摘要
您已在本 Apache Storm 教學課程中藉由 Storm Starter 了解如何建立 Storm on HDInsight 叢集，以及如何使用 Storm 儀表板部署、監視和管理 Storm 拓撲。

## <a id="next"></a>接續步驟
* **HDInsight Tools for Visual Studio** ：類似稍早提到的 Storm 儀表板，HDInsight Tools 可讓您運用 Visual Studio 提交、監視及管理 Storm 拓撲。 HDInsight Tools 也能讓您建立 C# Storm 拓撲，並提供可讓您在叢集上部署和執行的範例拓撲。
  
    如需詳細資訊，請參閱 [開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。
* **範例檔案**：HDInsight Storm 叢集在 **%STORM_HOME%\contrib** 目錄中提供數個範例。 每個範例應該會包含下列項目：
  
  * 原始程式碼 - 例如，storm-starter-0.9.1.2.1.5.0-2057-sources.jar
  * Java 文件 - 例如，storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar
  * 範例 - 例如，storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar
    
    請使用 'jar' 命令來解壓縮原始程式碼或 Java 文件。 例如，'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'。
    
    > [!NOTE]
    > Java 文件是由網頁組成。 解壓縮之後，請使用瀏覽器來檢視 **index.html** 檔案。
    > 
    > 
    
    若要存取這些範例，必須為 Storm on HDInsight 叢集啟用「遠端桌面」，然後從 **%STORM_HOME%\contrib** 複製檔案。
* 以下文件含有可和 Storm on HDInsight 搭配使用的其他範例清單：
  
  * [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

