---
title: "Apache Storm on HDInsight 簡介 | Microsoft Docs"
description: "取得 Apache Storm 的簡介，並了解如何使用 Storm on HDInsight 在雲端中建置即時資料分析解決方案。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: 426c7f5860a4be45b4c8e7ff4dd4b4406715ea34


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Apache Storm on HDInsight 簡介：Hadoop 的即時分析

Apache Storm on HDInsight 可讓您使用 [Apache Hadoop](http://hadoop.apache.org)，在 Azure 環境中建立分散式的即時分析解決方案。

## <a name="what-is-apache-storm"></a>什麼是 Apache Storm？

Apache Storm 是一個容錯的分散式開放原始碼計算系統，可讓您使用 Hadoop 即時處理資料。 Storm 解決方案也能夠重播最初未成功處理的資料，保證一定會處理資料。

## <a name="why-use-storm-on-hdinsight"></a>為何使用 Storm on HDInsight？

Apache Storm on HDInsight 是已和 Azure 環境整合的受管理叢集。 HDInsight 上的 Storm 和其他 Hadoop 元件是以 Hortonworks Data Platform (HDP) 為基礎，而叢集的作業系統則是 Ubuntu (Linux 散發套件)。 這提供了非常適合 Hadoop 生態系統中受歡迎工具和服務的平台。

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

Apache Storm on HDInsight 提供下列主要優點︰

* 可做為受管理服務執行，且具有 99.9% 運作時間的 SLA。

* 在建立期間或之後針對叢集執行指令碼，輕鬆進行自訂。 如需詳細資訊，請參閱[使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

* 使用您選擇的語言：提供以 **Java**、**C#** 和 **Python** 撰寫的 Storm 元件支援。
  
  * Visual Studio 與 HDInsight 的整合，可供開發、管理及監視 C# 拓撲。 如需詳細資訊，請參閱[使用 HDInsight Tools for Visual Studio 開發 C# Storm 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

  * 支援 **Trident** Java 介面。 此介面可讓您建立 Storm 拓撲，以支援「只一次性」處理訊息、「交易式」資料存放區持續性和一組常用的串流分析作業。

* 輕鬆進行叢集的相應增加和相應減少：新增或移除背景工作節點，而不影響執行 Storm 拓撲。

* 與其他 Azure 服務整合，包括事件中樞 (Event Hub)、Azure 虛擬網路、SQL Database、Blob 儲存體和 DocumentDB。
  
  * 使用 Azure 虛擬網路安全地結合多個 HDInsight 叢集的功能：建立使用 HDInsight、HBase 或 Hadoop 叢集的分析管線。

如需使用 Apache Storm 做為即時分析解決方案的公司清單，請參閱 [使用 Apache Storm 的公司](https://storm.apache.org/documentation/Powered-By.html)(英文)。

若要開始使用 Storm，請參閱[開始使用 Storm on HDInsight][gettingstarted]。

### <a name="ease-of-provisioning"></a>輕鬆佈建

只要花數分鐘即可佈建新的 Storm on HDInsight 叢集。 指定叢集名稱、大小、管理帳戶儲存體帳戶。 Azure 即會建立叢集，包括範例拓撲和 Web 管理儀表板。

> [!NOTE]
> 您也可以使用 [Azure CLI](../xplat-cli-install.md) 或 [Azure PowerShell](/powershell/azureps-cmdlets-docs). 佈建 Storm 叢集。

提交要求後，15 分鐘內您就會有一個新的執行中 Storm 叢集，並準備好讓您進行第一次即時分析管線。

### <a name="ease-of-use"></a>容易使用

* __安全殼層連線__：您可以使用 SSH 透過網際網路存取 HDInsight 叢集的前端節點。 這可讓您直接在叢集上執行命令。

  如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

* __Web 連線__：HDInsight 叢集會提供 Ambari Web UI。 這可讓您輕鬆地監視、設定及管理叢集上的服務。 Storm on HDInsight 也提供 Storm UI，這可讓您從瀏覽器監視及管理執行中的 Storm 拓撲。

  如需詳細資訊，請參閱[使用 Ambari Web UI 管理 HDInsight](hdinsight-hadoop-manage-ambari.md)和[使用 Storm UI 進行監視和管理](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-using-the-storm-ui)。

* __Azure PowerShell 和 CLI__︰Azure PowerShell 和 Azure CLI 兩者都提供您可以從用戶端系統使用的命令列公用程式，以便搭配 HDInsight 和其他 Azure 服務運作。

* __Visual Studio 整合__：Data Lake Tools for Visual Studio (先前稱為 HDInsight Tools for Visual Studio) 包含可供建立 C# Storm 拓撲的專案範本，以及 Storm on HDInsight 的工具。 您可以從 Visual Studio 建立、部署、監視及管理 C# 拓撲。

  如需詳細資訊，請參閱[使用 HDInsight Tools for Visual Studio 開發 C# Storm 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

* __與其他 Azure 服務整合__

  * 對於 __Java__ 開發，Microsoft 會利用現有的 Storm 元件來與其他 Azure 服務整合 (可能的話)。 在某些情況下，可能需要服務特有的元件或解決方案。

    * __Azure Data Lake Store__：Java 型拓撲可以使用 Storm-HDFS Bolt 搭配 `adl://` 的 URI 配置來存取 Data Lake Store。 如需使用 Storm-HDFS Bolt 的範例，請參閱[使用 Azure Data Lake Store 搭配 Apache Storm on HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-write-data-lake-store)。

    * __Azure Blob 儲存體__ (做為 HDInsight 的儲存體時)︰使用 Storm-HDFS Bolt 搭配 `wasb://` 的 URI 配置，Java 型拓撲即可存取與叢集相關聯的 Azure Blob 儲存體。

    * __Azure 事件中樞__︰可以使用 Microsoft 所提供的 EventHubSpout 和 EventHubBolt 元件進行存取。 這些元件是以 Java 撰寫，並以獨立的 .jar 檔案形式提供。

    如需開發 Java 解決方案的詳細資訊，請參閱[開發 Storm on HDInsight 的 Java 型拓撲](hdinsight-storm-develop-java-topology.md)。

  * 對於 __C#__ 開發，您通常可以使用適用於 Azure 服務的 .NET SDK。 在某些情況下，SDK 可以依賴 Linux 上無法使用的架構 (適用於 HDInsight 3.4 及更新版本的主機 OS)。在此情況下，您可以在 C# 解決方案中使用 Java 元件。

    * 使用 __SQL DB__、__DocumentDB__、__EventHub__ 和 __HBase__ 的範例已納入為 Azure Data Lake Tools for Visual Studio 中的範本。 如需詳細資訊，請參閱[開發 Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

    * __Azure 事件中樞__：如需從 C# 解決方案使用 Java 元件的範例，請參閱[使用 Storm on HDInsight 處理 Azure 事件中樞的事件 (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)。

    如需開發 Java 解決方案的詳細資訊，請參閱[開發 Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

### <a name="reliability"></a>可靠性

即使資料分析分散在數以百計的節點，Apache Storm 也能保證每個傳入訊息一律都會經過完整處理。

**Nimbus 節點**提供與 Hadoop JobTracker 類似的功能，並會透過 **Zookeeper** 指派工作給叢集中的其他節點。 Zookeeper 節點可為叢集進行協調，並促進 Nimbus 與背景工作節點上 **監督員** 處理序之間的通訊。 如果其中一個處理節點停止，Nimbus 節點會收到通知，然後將工作和相關資料指派給其他節點。

Apache Storm 的預設組態只有一個 Nimbus 節點。 Storm on HDInsight 會執行兩個 Nimbus 節點。 如果主要節點失敗，HDInsight 叢集會切換至次要節點，直到主要節點復原為止。

![Nimbus、Zookeeper 和監督員的圖表](./media/hdinsight-storm-overview/nimbus.png)

### <a name="scale"></a>調整

雖然您可以在建立時指定叢集的節點數，但您可以擴大或縮小叢集以配合工作負載。 即使在處理資料期間，所有 HDInsight 叢集均可讓您變更叢集中的節點數。

> [!NOTE]
> 若要利用透過調整所加入的新節點，您必須重新平衡在叢集大小增加之前啟動的拓撲。

### <a name="support"></a>支援

Storm on HDInsight 隨附完整的企業級全年無休支援。 Storm on HDInsight 也有 99.9% 的 SLA。 這表示我們保證叢集至少 99.9% 的時間具有外部連線能力。

## <a name="common-use-cases-for-real-time-analytics"></a>即時分析的常見使用案例

以下是一些 Apache storm on HDInsight 可能的常見使用案例。 如需真實案例的相關資訊，請參閱 [公司如何使用 Storm](https://storm.apache.org/documentation/Powered-By.html)(英文)。

* 物聯網 (IoT)
* 詐騙偵測
* 社交分析
* 擷取、轉換、載入 (ETL)
* 網路監視
* 搜尋
* Mobile Engagement

## <a name="how-is-data-in-hdinsight-storm-processed"></a>如何處理 HDInsight Storm 中的資料？

Apache Storm 會執行 **拓撲** ，而不是您在 HDInsight 或 Hadoop 中可能熟悉的 MapReduce 工作。 Storm on HDInsight 叢集包含兩種節點：執行 **Nimbus** 的前端節點和執行**監督員**的背景工作節點。

* **Nimbus**：類似 Hadoop 中的 JobTracker，負責將程式碼散發到整個叢集、指派工作給虛擬機器及監控失敗情況。 HDInsight 提供兩個 Nimbus 節點，所以 Storm on HDInsight 不會有單一失敗點。
* **監督員**：每一個背景工作節點的監督員負責啟動和停止節點上的**背景工作處理序**。
* **背景工作處理序**：執行一部分的**拓撲**。 執行中的拓撲分散在整個叢集的許多背景工作處理序上。
* **拓撲**：定義一份可處理資料**串流**的計算圖。 與 MapReduce 工作不同之處在於，拓撲會一直執行到您將它們停止為止。
* **串流**：未繫結的 **Tuple** 集合。 串流由 **pout** 和 **Bolt** 產生，由 **Bolt** 取用。
* **Tuple**：動態類型值的具名清單。
* **Spout**：取用資料來源的資料，並發出一或多個**串流**。
  
  > [!NOTE]
  > 在許多情況下，會自 Kafka 或 Azure 事件中樞等佇列中讀取資料。 佇列可確保運作中斷時資料仍持續存在。

* **Bolt**：取用**串流**、在 **Tuple** 上執行處理，然後可能發出**串流**。 Bolt 也負責將資料寫入外部儲存體，例如佇列、HDInsight、HBase、Blob 或其他資料存放區。
* **Apache Thrift**：一套軟體架構，支援可擴充的跨語言服務開發。 它可讓您建置 C++、Java、Python、PHP、Ruby、Erlang、Perl、Haskell、C#、Cocoa、JavaScript、Node.js、Smalltalk 和其他語言之間通用的服務。
  
  * **Nimbus** 是一種 Thrift 服務，而**拓撲**是 Thrift 定義，因此可利用各種程式設計語言來開發拓撲。

如需關於 Storm 元件的詳細資訊，請參閱 apache.org 上的 [Storm 教學課程][apachetutorial]。

## <a name="what-programming-languages-can-i-use"></a>我可以使用哪些程式設計語言？

Storm on HDInsight 叢集支援 C#、Java 和 Python。

### <a name="c35"></a>C&#35;

Data Lake Tools for Visual Studio 可讓 .NET 開發人員以 C# 語言設計和實作拓撲。 您也可以建立使用 Java 和 C# 元件的混合式拓撲。

如需詳細資訊，請參閱 [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

### <a name="java"></a>Java

您遇到的大多數 Java 範例都是純 Java 或 Trident。 Trident 是一種高階抽象概念，可讓工作變得更簡單，例如聯結、彙總、分組和篩選。 不過，Trident 是以 Tuple 批次為對象，而原始 Java 解決方案是以一次一個 Tuple 的方式來處理串流。

如需關於 Trident 的詳細資訊，請參閱 apache.org 上的 [Trident 教學課程](https://storm.apache.org/documentation/Trident-tutorial.html) (英文)。

如需 Java 與 Trident 拓撲的範例，請參閱 [範例 Storm 拓撲清單](hdinsight-storm-example-topology.md) 或 HDInsight 叢集上的 Storm-Starter 範例。

Storm-Starter 範例位於 HDInsight 叢集上的 ** /usr/hdp/current/storm-client/contrib/storm-starter** 目錄。

## <a name="what-are-some-common-development-patterns"></a>有哪些常見的開發模式？

### <a name="guaranteed-message-processing"></a>保證處理訊息

Storm 可以提供不同程度的訊息處理保證。 例如，基本的 Storm 應用程式可以保證至少處理一次，而 Trident 可以保證只處理一次。

如需詳細資訊，請參閱 apache.org 上的 [保證處理資料](https://storm.apache.org/about/guarantees-data-processing.html) (英文)。

### <a name="ibasicbolt"></a>IBasicBolt

讀取輸入 Tuple、發出零個以上的 Tuple，然後在執行方法結束時立即認可輸入 Tuple，由於這種模式實在相當常見，所以 Storm 提供 [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) 介面將此模式自動化。

### <a name="joins"></a>聯結

聯結兩個資料串流的方式隨著應用程式而有所不同。 例如，您可以將多個串流中的每個 Tuple 聯結成一個新的串流，或只聯結特定時間範圍的幾批 Tuple。 無論何者，都可利用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)來完成聯結，這是一種定義 Tuple 如何路由傳送至 bolt 的方法。

在下列 Java 範例中，會使用 fieldsGrouping 將源自元件 "1"、"2" 和 "3" 的 Tuple 路由傳送至 **MyJoiner** bolt。

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>批次處理

批次處理有多種方式。 在 C# 或 Java 拓撲中，可以先使用簡單的計數器來批次處理 X 個 Tuple，再將它們發出，或使用稱為「計時 Tuple」的內部計時機制，每 X 秒發出一批。

如需使用 C# 元件中 tick tuple 的範例，請參閱[PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java)。

如果您使用 Trident，則是以 Tuple 批次處理為基礎。

### <a name="caching"></a>快取

通常會使用記憶體內部快取當做加速處理的機制，因為此方式可以將常用的資產保留在記憶體內。 由於拓撲分散於多個節點上，以及每個節點內的多個程序上，因此您應該考慮使用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) ，以確保含有用於快取查閱欄位的 Tuple，一定會路由傳送至相同的程序。 這樣可避免快取項目在程序之間重複。

### <a name="streaming-top-n"></a>串流前 N 個

當拓撲取決於計算 "top N" 值時，例如 Twitter 的前 5 個趨勢，您應該平行地計算前 N 值，然後將這些計算的輸出合併成一個全域值。 這可利用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) 來進行，依欄位路由傳送至平行 bolt (此時會依欄位值分割資料)，然後路由傳送至 bolt 來整體決定前 N 值。

如需相關範例，請參閱 [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) 範例。

## <a name="what-type-of-logging-does-storm-use"></a>Storm 使用何種類型的記錄？

Storm 使用 Apache Log4j 來記錄資訊。 預設會記錄大量的資料，因此難以排序整個資訊。 您可以將記錄組態檔納入 Storm 拓撲的一部分，以便控制記錄行為。

如需示範如何設定記錄的拓撲範例，請參閱 Storm on HDInsight 的 [以 Java 為基礎的 WordCount](hdinsight-storm-develop-java-topology.md) 範例。

## <a name="next-steps"></a>後續步驟

深入了解使用 Apache Storm in HDInsight 的即時分析解決方案：

* [開始使用 Storm on HDInsight][gettingstarted]
* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md



<!--HONumber=Feb17_HO2-->


