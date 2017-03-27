---
title: "在事件中樞內使用 Storm on HDInsight 來處理事件 | Microsoft Docs"
description: "了解如何利用在 Visual Studio 中使用 HDInsight Tools for Visual Studio 所建立之 C# Storm 拓撲來處理事件中樞資料。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 23bdde763de6f437a0dec74c51722cbcfc19b141
ms.lasthandoff: 03/07/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>利用 Storm on HDInsight 處理 Azure 事件中樞的事件 (C#)

Azure 事件中樞可讓您從網站、應用程式和裝置處理巨量資料。 事件中樞 Spout 可讓您輕鬆地使用 Apache Storm on HDInsight 來即時分析資料。 您也可以使用事件中樞 Bolt 將資料從 Storm 寫入事件中樞。

在本教學課程中，您將了解如何使用隨 HDInsight Tools for Visual Studio 一起安裝的 Visual Studio 範本，以建立適用於 Azure 事件中樞的兩個拓撲。

* **EventHubWriter**：隨機產生資料並將資料寫入事件中樞
* **EventHubReader**︰從事件中樞讀取資料並將資料記錄到 Storm 記錄檔

> [!NOTE] 
> 如需本專案的 Java 版本，請參閱[使用 Storm on HDInsight 處理 Azure 事件中樞的事件 (Java)](hdinsight-storm-develop-java-event-hub-topology.md)。

## <a name="scpnet"></a>SCP.NET

這些專案使用 SCP.NET，其為 NuGet 封裝，可方便您建立 C# 拓撲和元件來與 Storm on HDInsight 搭配使用。

> [!IMPORTANT]
> 雖然本文件中的步驟依賴 Windows 開發環境與 Visual Studio，但已編譯的專案可以提交到使用 Linux 之 HDInsight 叢集上的 Storm。 __只有在 2016/10/28 之後建立的以 Linux 為基礎的叢集可支援 SCP.NET 拓撲。__

### <a name="cluster-versioning"></a>叢集版本控制

專案所使用的 Microsoft.SCP.Net.SDK NuGet 封裝必須符合安裝在 HDInsight 上的 Storm 主要版本。 HDInsight 3.3 版及 3.4 版上的 Storm 使用 Storm 0.10.x 版，因此您必須搭配使用 SCP.NET 0.10.x.x 版與這些叢集。 HDInsight 3.5 使用 Storm 1.0.x，因此您必須搭配使用 SCP.NET 1.0.x.x 版與此叢集版本。

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

HDInsight 3.4 和更高版本使用單聲道來執行 C# 拓撲。 大多能搭配 Mono 正常運作。 不過您應該查看 [Mono 相容性](http://www.mono-project.com/docs/about-mono/compatibility/)文件，以了解是否可能有不相容之處。

C# 拓撲也必須以 .NET 4.5 為目標。

## <a name="how-to-work-with-event-hubs"></a>如何使用事件中樞

Microsoft 提供一組可用來從 Storm 拓撲與 Azure 事件中樞通訊的 Java 元件。 您可以在此找到包含在這些元件最新版本的 jar 檔案：[https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/)。

> [!IMPORTANT]
> 雖然元件是以 Java 所撰寫，您可以輕鬆地從 C# 拓撲使用它們。

在此範例中會用到下列元件︰

* __EventHubSpout__：從事件中樞讀取資料。
* __EventHubBolt__：將資料寫入事件中樞。
* __EventHubSpoutConfig__：用來設定 EventHubSpout。
* __EventHubBoltConfig__：用來設定 EventHubBolt。
* __UnicodeEventDataScheme__：用來設定從事件中樞讀取時使用 UTF-8 編碼的 Spout。 預設的編碼方式為字串。

### <a name="example-spout-usage"></a>範例 Spout 使用方式

SCP.NET 會提供將 EventHubSpout 新增至您拓撲的方法。 這些方法比使用泛型方法新增 Java 元件更容易新增 Spout。 下列範例示範如何使用 SCP.NET 所提供的 __SetEventHubSpout__ 和 EventHubSpoutConfig 方法建立 Spout︰

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the Event Hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The Event Hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the Event Hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

前一個範例會建立名為 __EventHubSpout__ 的新 Spout 元件，並設定它與事件中樞通訊。 元件的平行處理原則提示設定為事件中樞的資料分割數目。 此設定可讓 Storm 針對每個資料分割建立元件執行個體。

> [!WARNING]
> 至 2017 年 1 月 1 日止，從事件中樞讀取資料時，使用 SetEventHubSpout 和 EventHubSpoutConfig 方法建立使用字串編碼之 Spout。

建立 Spout 時，您也可以使用泛型 JavaComponentConstructor 方法。 下列範例示範如何使用 JavaComponentConstructor 方法建立 Spout。 它也會示範如何將 Spout 設定為讀取使用 UTF-8 編碼而非字串的資料。

```csharp
// Create an instance of UnicodeEventDataScheme
var schemeConstructor = new JavaComponentConstructor("com.microsoft.eventhubs.spout.UnicodeEventDataScheme");
// Create an instance of EventHubSpoutConfig
var eventHubSpoutConfig = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpoutConfig",
    new List<Tuple<string, object>>()
    {
        // the shared access signature name and key used to read the data
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"]),
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKey"]),
        // The namespace that contains the Event Hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The Event Hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the Event Hub
        Tuple.Create<string, object>("int", eventHubPartitions),
        // The encoding scheme to use when reading
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.IEventDataScheme", schemeConstructor)
    }
    );
// Create an instance of the spout
var eventHubSpout = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpout",
    new List<Tuple<string, object>>()
    {
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.EventHubSpoutConfig", eventHubSpoutConfig)
    }
    );
// Set the spout in the topology
topologyBuilder.SetJavaSpout("EventHubSpout", eventHubSpout, eventHubPartitions);
```

> [!IMPORTANT]
> UnicodeEventDataScheme 僅在事件中樞元件 9.5 版中可用，其可從 [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/) 取得。

### <a name="example-bolt-usage"></a>範例 Bolt 使用方式

使用 JavaComponmentConstructor 方法來建立 bolt 的執行個體。 下列範例示範如何建立及設定 EventHubBolt 的新執行個體︰

```csharp
//Create constructor for the Java bolt
JavaComponentConstructor constructor =
    // Use a Clojure expression to create the EventHubBoltCOnfig
    JavaComponentConstructor.CreateFromClojureExpr(
        String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
    // The policy name and key used to read from Event Hubs
    ConfigurationManager.AppSettings["EventHubPolicyName"],
    ConfigurationManager.AppSettings["EventHubPolicyKey"],
    // The namespace that contains the Event Hub
    ConfigurationManager.AppSettings["EventHubNamespace"],
    "servicebus.windows.net", //suffix for the namespace fqdn
    // The Evetn Hub Name)
    ConfigurationManager.AppSettings["EventHubName"],
    "true"));

//Set the bolt
topologyBuilder.SetJavaBolt(
        "EventHubBolt",
        constructor,
        partitionCount). //Parallelism hint uses partition count
    shuffleGrouping("Spout"); //Consume data from spout
```

> [!NOTE]
> 這個範例會使用以字串傳遞的 Clojure 運算式，而不是如 Spout 範例一樣使用 JavaComponentConstructor 來建立個別的 EventHubBoltConfig。 兩種方法均可。 使用覺得較適合您的方法。

## <a name="download-the-completed-project"></a>下載完成的專案

您可以從 GitHub 下載本教學課程中所建立之專案的完整版本： [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)。 不過，您仍需要遵循本教學課程中的步驟，提供組態設定。

### <a name="prerequisites"></a>必要條件

* [Apache Storm on HDInsight cluster 3.5 版](hdinsight-apache-storm-tutorial-get-started.md)

    > [!WARNING]
    > 此文件中所使用的範例需要 Storm on HDInsight version 3.5 版。 由於重大類別名稱變更，這不適用舊版的 HDInsight。 如需可搭配舊版叢集使用的此範例，請參閱 [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases)。

* [Azure 事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Azure .NET SDK](http://azure.microsoft.com/downloads/)

* [HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

* 開發環境為 Java JDK 1.7 或更新版本。 JDK 下載可從 [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 取得。

  * **JAVA_HOME** 環境變數必須指向包含 Java 的目錄。
  * **%JAVA_HOME%/bin** 目錄必須在路徑中

## <a name="download-the-event-hub-components"></a>下載事件中樞元件

Spout 和 Bolt 會以名為 **eventhubs-storm-spout-#.#-jar-with-dependencies.jar** 的單一 Java 封存 (.jar) 檔案形式散發，其中 #.# 是檔案的版本。

若要使用此解決方案搭配 HDInsight 3.5，請使用 [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/) 提供的版本 0.9.5 jar 檔案。

建立名為 `eventhubspout` 的目錄並將檔案儲存到該目錄。

## <a name="configure-event-hubs"></a>設定事件中樞

事件中樞是此範例的資料來源。 請使用[開始使用事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)文件之**建立事件中樞**區段中的資訊。

1. 在建立事件中樞之後，檢視 Azure 入口網站中的 [事件中樞] 刀鋒視窗，然後選取 [共用存取原則]。 選取 [+ 新增] 連結來新增下列原則︰
   
   | 名稱 | 權限 |
   | --- | --- |
   | 寫入器 |傳送 |
   | 讀取器 |接聽 |
   
    ![原則](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. 選取 [讀取器] 和 [寫入器] 原則。 複製並儲存這兩個原則的 [主索引鍵] 值，因為稍後將使用這些值。

## <a name="configure-the-eventhubwriter"></a>設定 EventHubWriter

1. 如果您尚未安裝最新版本的 HDInsight Tools for Visual Studio，請參閱[開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。

2. 從 [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) 下載方案。

3. 在 **EventHubWriter** 專案中，開啟 **App.config** 檔案。 使用您稍早設定之事件中樞的資訊填入下列索引鍵的值︰
   
   | 索引鍵 | 值 |
   | --- | --- |
   | EventHubPolicyName |寫入器 (如果您為具有*傳送*權限的原則使用了不同名稱，請改用該名稱。) |
   | EventHubPolicyKey |寫入器原則的索引鍵 |
   | EventHubNamespace |包含事件中樞的命名空間 |
   | EventHubName |事件中樞名稱 |
   | EventHubPartitionCount |事件中樞內的資料分割數目 |

4. 儲存並關閉 **App.config** 檔案。

## <a name="configure-the-eventhubreader"></a>設定 EventHubReader

1. 開啟 **EventHubReader** 專案。

2. 開啟 **EventHubReader** 的 **App.config**。 使用您稍早設定之事件中樞的資訊填入下列索引鍵的值︰
   
   | 索引鍵 | 值 |
   | --- | --- |
   | EventHubPolicyName |讀取器 (如果您為具有*接聽*權限的原則使用了不同名稱，請改用該名稱。) |
   | EventHubPolicyKey |讀取器原則的索引鍵 |
   | EventHubNamespace |包含事件中樞的命名空間 |
   | EventHubName |事件中樞名稱 |
   | EventHubPartitionCount |事件中樞內的資料分割數目 |

3. 儲存並關閉 **App.config** 檔案。

## <a name="deploy-the-topologies"></a>部署拓撲

1. 在**方案總管**中，以滑鼠右鍵按一下 [EventHubReader] 專案，然後選取 [提交到 Storm on HDInsight]。
   
    ![提交到 Storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. 在 [提交拓撲] 畫面中，選取您的 [Storm 叢集]。 展開 [其他組態]，依序選取 [Java 檔案路徑]、[...]，然後選取包含您稍早下載之 jar 檔的目錄。 最後，按一下 [提交]。
   
    ![提交對話方塊的影像](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. 提交拓撲之後，[Storm 拓撲檢視器] 便會隨即出現。 若要檢視拓撲的相關資訊，請選取左窗格中的 **EventHubReader** 拓撲。
   
    ![範例儲存體檢視](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. 在**方案總管**中，以滑鼠右鍵按一下 [EventHubWriter] 專案，然後選取 [提交到 Storm on HDInsight]。

5. 在 [提交拓撲] 畫面中，選取您的 [Storm 叢集]。 展開 [其他組態]，依序選取 [Java 檔案路徑]、[...]，然後選取包含您稍早下載之 jar 檔的目錄。 最後，按一下 [提交]。

6. 提交拓撲之後，請重新整理 [Storm 拓撲檢視器]  中的拓撲清單，以確認兩個拓撲皆在叢集上執行。

7. 在 [Storm 拓撲檢視器] 中，選取 [EventHubReader] 拓撲。

8. 若要開啟 Bolt 的**元件摘要**，請按兩下圖表中的 **LogBolt** 元件。

9. 在 [執行程式] 區段中，選取 [連接埠] 資料行內的其中一個連結。 這會顯示該元件記錄的資訊。 所記錄的資訊類似下列文字︰
   
        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>停止拓撲

若要停止拓撲，請在 [Storm 拓撲檢視器] 中選取每個拓撲，然後選取 [終止]。

![終止拓撲的影像](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>後續步驟

在本文件中，您已經了解如何使用 Java 事件中樞 Spout 和 Bolt，以利用 C# 拓撲來使用 Azure 事件中樞的資料。 若要深入了解如何建立 C# 拓撲，請參閱下列文件：

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [SCP 程式設計指南](hdinsight-storm-scp-programming-guide.md)
* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)


