---
title: "搭配 Apache Storm 開始使用以 Java 撰寫的事件中樞 | Microsoft Docs"
description: "遵循此教學課程，開始使用 Azure Event Hubs 以 Java 傳送事件並且在 Apache Storm 叢集中接收這些事件。"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 385869bd-1ebe-44ae-8113-cc4679a568eb
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 52c9ded8af99c5ae0836be8e71394dd1e5072aff
ms.openlocfilehash: 7566a8d0643607ee80c056ed4410aefd930226b8


---
# <a name="get-started-with-event-hubs"></a>開始使用事件中心
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>簡介
事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。 收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱[事件中樞概觀][Event Hubs Overview]。

本教學課程說明如何使用以 Java 撰寫的主控台應用程式將訊息收集到事件中心，以及如何使用 Apache Storm 平行擷取它們。

若要完成本教學課程，您需要下列項目：

* 為了執行 [Maven](http://maven.apache.org/)所設定的 Java 開發環境。 針對本教學課程，我們採用 [Eclipse](https://www.eclipse.org/)。
* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>執行應用程式
現在您已經準備好執行應用程式。

1. 從 Eclipse 執行 **LogTopology** 類別，然後等它啟動所有資料分割的接收器。
2. 執行 **Sender** 專案，在主控台視窗中按下 **Enter** 鍵，並查看出現在接收器視窗中的事件。
   
    ![][22]

> [!NOTE]
> 本教學課程中只使用本機模式的 Storm 進行開發。 如需 Storm 部署和模式的詳細資訊，請參閱 [HDInsight Storm 概觀][HDInsight Storm Overview]和 [Apache Storm][Apache Storm] 官方文件。
> 
> 

## <a name="next-steps"></a>後續步驟
下列資源可供開發整合事件中心和 Storm 的應用程式。

* [使用 Storm 和 HDInsight 分析感應器資料]是完整的案例教學課程，其中使用事件中樞、Storm 和 HBase 將感應器資料內嵌至 Hadoop 叢集。
* [在 Storm 和 HDInsight 上使用 SCP.NET 和 C# 開發串流資料處理應用程式][Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]教學課程說明如何使用 C# 撰寫 Storm 管線。

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs Overview]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm Overview]: ../hdinsight/hdinsight-storm-overview.md
[使用 Storm 和 HDInsight 分析感應器資料]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Dec16_HO2-->


