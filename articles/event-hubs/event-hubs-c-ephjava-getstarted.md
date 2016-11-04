---
title: 開始使用以 C 撰寫的事件中樞 | Microsoft Docs
description: 遵循此教學課程，以開始使用 Azure 事件中樞；以 C 傳送事件並使用事件處理器主機以 Java 接收事件。
services: event-hubs
documentationcenter: ''
author: jtaubensee
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 09/27/2016
ms.author: jotaub;sethm

---
# <a name="get-started-with-event-hubs"></a>開始使用事件中心
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>簡介
事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。 收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱 [事件中樞概觀][事件中樞概觀]。

在本教學課程中，您將學習如何使用以 C# 撰寫的主控台應用程式將訊息擷取到事件中樞，以及使用 C# [事件處理器主機][事件處理器主機] 程式庫平行擷取它們。

若要完成本教學課程，您需要下列項目：

* C 開發環境。 在本教學課程中，我們假設 [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) 上的 gcc 堆疊有 Ubuntu 14.04。 其他環境的指示將會在外部連結中提供。
* Microsoft Visual Studio Express for Windows
* 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費帳戶。 如需詳細資料，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>執行應用程式
現在您已經準備好執行應用程式。

1. 執行 **Receiver** 專案。
   
   ![][21]
2. 執行 **Sender** 程式，並注意接收器視窗中出現的事件。
   
   ![][24]

## <a name="next-steps"></a>後續步驟
您已經建置工作應用程式，可建立「事件中樞」和傳送及接收資料，接下來可進行下列案例：

* [使用事件中樞的完整範例應用程式][使用事件中樞的完整範例應用程式]。
* [使用「事件中樞」相應放大事件處理][使用「事件中樞」相應放大事件處理] 範例。
* [事件中心概觀][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[事件處理器主機]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件中樞概觀]: event-hubs-overview.md
[使用事件中樞的完整範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[使用「事件中樞」相應放大事件處理]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Oct16_HO2-->


