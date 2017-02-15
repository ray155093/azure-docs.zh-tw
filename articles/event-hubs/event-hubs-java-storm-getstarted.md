---
title: "搭配 Apache Storm 開始使用以 Java 撰寫的事件中樞 | Microsoft Docs"
description: "遵循此教學課程，開始使用 Azure Event Hubs 以 Java 傳送事件並且在 Apache Storm 叢集中接收這些事件。"
services: event-hubs
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 385869bd-1ebe-44ae-8113-cc4679a568eb
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eacafbcddb81085c706d8c4b64c426b21ce06b79


---
# <a name="get-started-with-event-hubs"></a>開始使用事件中心
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>簡介
事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。 收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱[事件中樞概觀][事件中樞概觀]。

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
> 本教學課程中只使用本機模式的 Storm 進行開發。 如需 Storm 部署和模式的詳細資訊，請參閱 [HDInsight Storm 概觀][HDInsight Storm 概觀]和 [Apache Storm][Apache Storm] 官方文件。
> 
> 

## <a name="next-steps"></a>後續步驟
下列資源可供開發整合事件中心和 Storm 的應用程式。

* [使用 Storm 和 HDInsight 分析感應器資料] 是一個完整案例教學課程，該課程使用事件中心、Storm 和 HBase 擷取 Hadoop 叢集中的感應器資料。
* [在 Storm 和 HDInsight 上使用 SCP.NET 和 C# 開發串流資料處理應用程式][在 Storm 和 HDInsight 上使用 SCP.NET 和 C# 開發串流資料處理應用程式]教學課程說明如何使用 C# 撰寫 Storm 管線。

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[事件處理器主機]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件中樞概觀]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm 概觀]: ../hdinsight/hdinsight-storm-overview.md
[使用 Storm 和 HDInsight 分析感應器資料]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[在 Storm 和 HDInsight 上使用 SCP.NET 和 C# 開發串流資料處理應用程式]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Nov16_HO3-->


