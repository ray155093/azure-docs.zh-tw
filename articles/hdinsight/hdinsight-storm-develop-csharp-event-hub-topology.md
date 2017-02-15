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
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>利用 Storm on HDInsight 處理 Azure 事件中樞的事件 (C#)
Azure 事件中樞可讓您從網站、應用程式和裝置處理巨量資料。 事件中樞 Spout 可讓您輕鬆地使用 Apache Storm on HDInsight 來即時分析資料。 您也可以使用事件中樞 Bolt 將資料從 Storm 寫入事件中樞。

在本教學課程中，您將了解如何使用隨 HDInsight Tools for Visual Studio 一起安裝的 Visual Studio 範本，以建立適用於 Azure 事件中樞的兩個拓撲。

* **EventHubWriter**：隨機產生資料並將資料寫入事件中樞
* **EventHubReader**︰從事件中樞讀取資料並將資料記錄到 Storm 記錄檔

> [!NOTE]
> 雖然本文件中的步驟依賴 Windows 開發環境與 Visual Studio，但已編譯的專案可以提交到以 Linux 或 Windows 為基礎的 HDInsight 叢集。 只有在 2016/10/28 之後建立的以 Linux 為基礎的叢集可支援 SCP.NET 拓撲。
> 
> 若要搭配使用 C# 拓撲與以 Linux 為基礎的叢集，您必須將專案使用的 Microsoft.SCP.Net.SDK NuGet 套件，更新為 0.10.0.6 版或更新版本。 套件版本也必須符合 HDInsight 上安裝的 Storm 主要版本。 例如，Storm on HDInsight 3.3 和 3.4 版使用 Storm 0.10.x 版，而 HDInsight 3.5 使用 Storm 1.0.x。
> 
> 在以 Linux 為基礎之叢集上的 C# 拓撲必須使用 .NET 4.5，並使用 Mono 以在 HDInsight 叢集上執行。 這些元件大多能正常運作，不過您應該查看 [Mono 相容性](http://www.mono-project.com/docs/about-mono/compatibility/)文件，以了解是否可能有不相容之處。
> 
> 如需此專案的 Java 版本 (同樣可在以 Linux 或 Windows 為基礎的叢集上運作)，請參閱[使用 Storm on HDInsight 處理 Azure 事件中樞的事件 (Java)](hdinsight-storm-develop-java-event-hub-topology.md)。
> 
> 

## <a name="prerequisites"></a>必要條件
* [Apache Storm on HDInsight 叢集](hdinsight-apache-storm-tutorial-get-started.md)
* [Azure 事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Azure .NET SDK](http://azure.microsoft.com/downloads/)
* [HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>已完成的專案
您可以從 GitHub 下載本教學課程中所建立之專案的完整版本： [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)。 不過，您仍需要遵循本教學課程中的步驟，提供組態設定。

## <a name="event-hubs-spout-and-bolt"></a>事件中樞 Spout 和 Bolt
事件中樞 Spout 和 Bolt 是可讓您輕鬆地從 Apache Storm 使用事件中樞的 Java 元件。 雖然這些元件是採用 Java 撰寫而成的，但 HDInsight Tools for Visual Studio 可讓您建立混合 C# 和 Java 元件的混合式拓撲。

Spout 和 Bolt 會以名為 **eventhubs-storm-spout-#.#-jar-with-dependencies.jar** 的單一 Java 封存 (.jar) 檔案形式散發，其中 #.# 是檔案的版本。

### <a name="download-the-jar-file"></a>下載 .jar 檔
[HDInsight Storm 範例](https://github.com/hdinsight/hdinsight-storm-examples)專案下的 **lib/eventhubs** 資料夾包含最新版的 jar 檔案。 若要下載檔案，請使用下列其中一種方法。

> [!NOTE]
> 已提交 Spout 和 Bolt，以將其納入 Apache Storm 專案中。 如需詳細資訊，請參閱 GitHub 中的 [STORM-583：Storm 事件中樞的初始簽入](https://github.com/apache/storm/pull/336/files)
> 
> 

* **下載 ZIP 檔案**：從 [HDInsight Storm 範例](https://github.com/hdinsight/hdinsight-storm-examples)網站，選取右窗格中的 [下載 ZIP] 下載包含專案的 .zip 檔案。
  
    ![下載 zip 按鈕](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    下載檔案後，您可以解壓縮此封存，則檔案便會出現在 **lib** 目錄中。
* **複製專案**：如果您已安裝 [Git](http://git-scm.com/)，請使用下列命令在本機複製儲存機制，然後在 **lib** 目錄中尋找檔案。
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

## <a name="configure-event-hubs"></a>設定事件中樞
事件中樞是此範例的資料來源。 請使用[開始使用事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)文件之**建立事件中樞**區段中的資訊。

1. 在建立事件中樞之後，檢視 Azure 入口網站中的 [事件中樞] 刀鋒視窗，然後選取 [共用存取原則]。 使用 [+新增] 項目新增下列原則︰
   
   | 名稱 | 權限 |
   | --- | --- |
   | 寫入器 |傳送 |
   | 讀取器 |接聽 |
   
    ![原則](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)
2. 選取 [讀取器] 和 [寫入器] 原則。 複製並儲存這兩個原則的 [主索引鍵] 值，以供稍後使用。

## <a name="configure-the-eventhubwriter"></a>設定 EventHubWriter
1. 如果您尚未安裝最新版本的 HDInsight Tools for Visual Studio，請參閱[開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。
2. 從 [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) 下載方案。 開啟方案，花點時間看過 **EventHubWriter** 專案的程式碼。
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
1. 開啟 **EventHubReader** 專案，花點時間看過程式碼。
2. 開啟 **EventHubWriter** 的 **App.config**。 使用您稍早設定之事件中樞的資訊填入下列索引鍵的值︰
   
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
2. 在 [提交拓撲] 畫面中，選取您的 [Storm 叢集]。 展開 [其他組態]，依序選取 [Java 檔案路徑]、[...]，然後選取包含您稍早下載之 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 檔的目錄。 最後，按一下 [提交]。
   
    ![提交對話方塊的影像](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. 提交拓撲之後，[Storm 拓撲檢視器]  便會隨即出現。 選取左窗格的 [EventHubReader]  拓撲，以檢視拓撲的統計資料。 目前，因為事件中樞尚未有事件寫入，所以應該什麼都不會發生。
   
    ![範例儲存體檢視](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. 在**方案總管**中，以滑鼠右鍵按一下 [EventHubWriter] 專案，然後選取 [提交到 Storm on HDInsight]。
5. 在 [提交拓撲] 畫面中，選取您的 [Storm 叢集]。 展開 [其他組態]，依序選取 [Java 檔案路徑]、[...]，然後選取包含您稍早下載之 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 檔的目錄。 最後，按一下 [提交]。
6. 提交拓撲之後，請重新整理 [Storm 拓撲檢視器]  中的拓撲清單，以確認兩個拓撲皆在叢集上執行。
7. 在 [Storm 拓撲檢視器] 中，選取 [EventHubReader] 拓撲。
8. 在圖形檢視中，按兩下 [LogBolt] 元件。 這會開啟 Bolt 的 [元件摘要] 頁面。
9. 在 [執行程式] 區段中，選取 [連接埠] 資料行內的其中一個連結。 這會顯示該元件記錄的資訊。 所記錄的資訊如下︰
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>停止拓撲
若要停止拓撲，請在 [Storm 拓撲檢視器] 中選取每個拓撲，然後選取 [終止]。

![終止拓撲的影像](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>刪除叢集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="notes"></a>注意事項
### <a name="checkpointing"></a>檢查點
EventHubSpout 會定期將其狀態設定檢查點到 Zookeeper 節點，這會儲存目前從佇列讀取之訊息的位移。 如此可允許元件在下列狀況中，從儲存的位移處開始接收訊息：

* 元件執行個體失敗，且已重新啟動。
* 您可藉由加入或移除節點來增大或壓縮叢集。
* 拓撲遭終止並 **使用相同名稱**重新啟動。

您也可以匯出及匯入保存的檢查點到 WASB (您的 HDInsight 叢集所使用的 Azure 儲存體)。要執行此步驟的指令碼位於 Storm on HDInsight 叢集的 **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**。

> [!NOTE]
> 路徑中的版本號碼可能不同，因為叢集上安裝的 Storm 版本可能會在未來變更。
> 
> 

此目錄中的指令碼有：

* **stormmeta_import.cmd**：將所有 Storm 中繼資料從叢集預設儲存體容器匯入到 Zookeeper。
* **stormmeta_export.cmd**：將所有 Storm 中繼資料從 Zookeeper 匯出到叢集預設儲存體容器。
* **stormmeta_delete.cmd**：從 Zookeeper 刪除所有 Storm 中繼資料。

匯出匯入可讓您在需要刪除叢集，但又想要在讓新的叢集回到線上時從中樞的目前位移繼續處理時，保存檢查點資料。

> [!NOTE]
> 由於資料已保存到預設儲存體容器，新的叢集 **必須** 使用與先前叢集相同的儲存體帳戶和容器。
> 
> 

## <a name="next-steps"></a>後續步驟
在本文件中，您已經了解如何使用 Java 事件中樞 Spout 和 Bolt，以利用 C# 拓撲來使用 Azure 事件中樞的資料。 若要深入了解如何建立 C# 拓撲，請參閱下列內容。

* [使用 Visual Studio 開發 Apache Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [SCP 程式設計指南](hdinsight-storm-scp-programming-guide.md)
* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


