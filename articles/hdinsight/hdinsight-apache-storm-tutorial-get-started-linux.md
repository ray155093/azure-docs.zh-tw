---
title: "Apache Storm 教學課程：在 HDInsight 上開始使用 Linux 架構的 Storm | Microsoft Docs"
description: "在 Linux 架構的 HDInsight 上使用 Apache Storm 和 Storm Starter 範例，開始分析巨量資料。 了解如何使用 Storm 即時處理資料。"
keywords: "apache storm,apache storm 教學課程,巨量資料分析,storm 入門"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 856043148b3fc28594850ae27bedd57d48292582


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Apache Storm 教學課程：在 HDInsight 上使用 Storm Starter 範例開始分析巨量資料

Apache Storm 是一個可處理資料串流的分散式、容錯、即時的運算系統。 在 Storm on Azure HDInsight 中，您可以建立雲端式 Storm 叢集，以執行即時的巨量資料分析。

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

您必須具備下列先決條件，才能順利完成本 Apache Storm 教學課程：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* **熟悉 SSH 和 SCP**。 如需搭配 HDInsight 使用 SSH 和 SCP 的詳細資訊，請參閱下列文章：
  
    * **Linux、Unix 或 OS X 用戶端**：請參閱[在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Windows 用戶端**：請參閱[從 Windows 在 HDInsight 上搭配使用 SSH (PuTTY) 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>存取控制需求

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>建立 Storm 叢集

在本節中，您將使用 Azure Resource Manager 範本建立 HDInsight 3.5 版叢集 (Storm 1.0.1 版)。 如需不同 HDInsight 版本及其 SLA 的相關資訊，請參閱〈 [HDInsight 元件版本設定](hdinsight-component-versioning.md)〉。 如需其他叢集建立方法，請參閱 [建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

1. 按一下以下影像，在 Azure 入口網站中開啟範本。         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight-35.json" target="_blank"><img src="./media/hdinsight-apache-storm-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    此範本位於公用 Blob 容器中，*https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*。 

2. 從 [自訂部署] 刀鋒視窗，輸入下列項目：
   
    * __資源群組__：在其中建立叢集的資源群組。

    * **叢集名稱**：Hadoop 叢集的名稱。

    * __叢集登入名稱__和__密碼__：預設的登入名稱是 admin。
    
    * __SSH 使用者名稱__和__密碼__：使用 SSH 連線到叢集時所需的使用者和密碼。

    * __位置__：叢集的地理位置。
     
     請記下這些值。  稍後在教學課程中需要這些資訊。
     
     > [!NOTE]
     > SSH 可透過命令列遠端存取 HDInsight 叢集。 您在此使用的使用者名稱和密碼會在透過 SSH 連接到叢集時使用。 此外，SSH 使用者名稱必須是唯一的，因為該名稱會在所有 HDInsight 叢集節點上建立使用者帳戶。 以下是一些保留給叢集上的服務使用的帳戶名稱，不能做為 SSH 使用者名稱︰
     > 
     > root、hdiuser、storm、hbase、ubuntu、zookeeper、hdfs、yarn、mapred、hbase、hive、oozie、falcon、sqoop、admin、tez、hcat、hdinsight-zookeeper。
     > 
     > 如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：
     > 
     > * [在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [從 Windows 在 HDInsight 上搭配使用 SSH (PuTTY) 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

3. 選取 [我同意上方所述的條款及條件]，按一下 [確定]，然後選取 [釘選到儀表板]

6. 按一下 [購買]。 您將會看到新的圖格，標題為「提交範本部署的部署」。 大約需要 20 分鐘的時間來建立叢集。

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>在 HDInsight 上執行 Storm Starter 範例

HDInsight 叢集已包含 [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) 範例。 在下列步驟中，您將執行 WordCount 範例。

1. 使用 SSH 連線到 HDInsight 叢集
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    如果您已經使用密碼保護您 SSH 使用者帳戶的安全，系統會提示您輸入密碼。 如果您使用的是公開金鑰，您可能必須使用 `-i` 參數來指定對應的私密金鑰。 例如， `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`。
   
    如需搭配使用 SSH 與以 Linux 為基礎的 HDInsight 的詳細資訊，請參閱下列文章：
   
    * [在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [從 Windows 在 HDInsight 上搭配使用 SSH (PuTTY) 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 使用下列命令以啟動範例拓撲：
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > 在舊版 HDInsight 上，拓撲的類別名稱是 `storm.starter.WordCountTopology` 而不是 `org.apache.storm.starter.WordCountTopology`。
   
    這會在叢集上使用 'wordcount' 的易記名稱，啟動範例 WordCount 拓撲。 命令會隨機產生句子，並計算句子中每個字詞的出現次數。
   
    > [!NOTE]
    > 將您自己的拓撲提交至叢集時，必須先複製包含叢集的 jar 檔案，再使用 `storm` 命令。 而您可以從檔案所在的用戶端使用 `scp` 命令來完成這個動作。 例如， `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > WordCount 範例和其他 Storm 入門範例都已經包含在叢集中，位置是 `/usr/hdp/current/storm-client/contrib/storm-starter/`。

如果您有興趣檢視 Storm 入門範例的來源，您可以在 [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter) 找到程式碼。 這個連結是 Storm 1.0.x，隨附於 HDInsight 3.5。 如需其他 Storm 版本，請使用頁面頂端的 [Branch] (分支) 按鈕來選取其他 Storm 版本。

## <a name="monitor-the-topology"></a>監視拓撲

Storm UI 提供 Web 介面來處理執行中的拓撲，包含在您的 HDInsight 叢集中。

使用下列步驟以 Storm UI 監視拓撲。

1. 開啟網頁瀏覽器並瀏覽至 https://CLUSTERNAME.azurehdinsight.net/stormui，其中 **CLUSTERNAME** 是叢集的名稱。 這會開啟 Storm UI。
    
    > [!NOTE]
    > 如果要求您提供使用者名稱和密碼，請輸入叢集系統管理員 (admin) 和建立叢集時使用的密碼。

2. 在 [拓撲摘要] 下，選取 [名稱] 欄中的 [wordcount] 項目。 這麼做會顯示拓撲的詳細資訊。
    
    ![包含 Storm Starter WordCount 拓樸資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    此頁面提供以下資訊：
    
    * **拓撲統計資料 (Topology stats)** ：拓撲效能的基本資訊，已整理為時間範圍。
     
        > [!NOTE]
        > 選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。

    * **Spouts** ：spout 的基本資訊，包括每個 spout 傳回的最後一個錯誤。
    
    * **Bolts** ：bolt 的基本資訊。
    
    * **拓撲組態 (Topology configuration)** ：拓撲組態的詳細資訊。
     
    此頁面也提供可對拓撲採取的動作：
   
    * **啟用** ：繼續處理已停用的拓撲。
    
    * **停用** ：暫停執行中拓撲。
    
    * **重新平衡** ：調整拓撲的平行處理原則。 變更叢集中的節點數目之後，您應該重新平衡執行中拓撲。 這可讓拓撲調整平行處理原則，以彌補叢集中增加/減少的節點數目。 如需詳細資訊，請參閱 [了解 Storm 拓撲的平行處理原則](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。
    
    * **終止 (Kill)** ：在指定的逾時之後終止 Storm 拓撲。

3. 在此頁面中，選取 [Spouts] 或 [Bolts] 區段中的一個項目。 如此會顯示所選元件的相關資訊。
   
    ![包含所選元件相關資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    此頁面會顯示以下資訊：
   
    * **Spout/Bolt 統計資料 (Spout/Bolt stats)** ：元件效能的基本資訊，已整理為時間範圍。
     
        > [!NOTE]
        > 選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。
     
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

## <a name="stop-the-topology"></a>停止拓撲

返回 word-count 拓撲的 [拓撲摘要] 頁面，然後選取 [拓撲動作] 區段中的 [終止] 按鈕。 出現提示時，請先輸入要等候 10 秒，再停止拓撲。 逾時期限過後，當您瀏覽儀表板的 [Storm UI]  區段時，便不會再顯示拓撲。

## <a name="delete-the-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="a-idnextanext-steps"></a><a id="next"></a>接續步驟

您已在本 Apache Storm 教學課程中藉由 Storm Starter 了解如何建立 Storm on HDInsight 叢集，以及如何使用 Storm 儀表板部署、監視和管理 Storm 拓撲。 接下來，了解如何 [使用 Maven 開發 Java 型拓撲](hdinsight-storm-develop-java-topology.md)。

如果您已熟悉開發 Java 型拓撲，而且想要將現有的拓撲部署至 HDInsight，請參閱 [部署和管理 HDInsight 上的 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)。

如果您是 .NET 開發人員，您可以使用 Visual Studio 建立 C# 或混合式 C#/Java 拓撲。 如需詳細資訊，請參閱 [使用 Visual Studio 的 Hadoop 工具開發 Apache Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

如需可搭配 Storm on HDInsight 使用的拓撲範例，請參閱下列範例︰

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO3-->


