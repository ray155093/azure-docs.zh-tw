---
title: "開始使用以 C 和 C# 撰寫的事件中樞 | Microsoft Docs"
description: "遵循此教學課程，以開始使用 Azure 事件中樞；傳送 C 中的事件並使用 EventProcessorHost 以 C# 接收事件。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5170c138-39ec-4eea-9925-e6902e5c425a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: fd7450985906dcf707a420d51b3a3f3b13786741
ms.openlocfilehash: 742bea46f7c4eba826523a49af4972dbb6db439b


---
# <a name="get-started-with-event-hubs"></a>開始使用事件中心
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>簡介
事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。 收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱[事件中樞概觀][Event Hubs overview]。

在本教學課程中，您將學習如何使用以 C# 撰寫的主控台應用程式將訊息內嵌至事件中樞，以及使用 C# [事件處理器主機][Event Processor Host]程式庫平行擷取它們。

若要完成本教學課程，您需要下列項目：

* C 開發環境。 在本教學課程中，我們假設 [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 上的 gcc 堆疊有 Ubuntu 14.04。 其他環境的指示將會在外部連結中提供。
* Microsoft Visual Studio Express for Windows
* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>執行應用程式
現在您已經準備好執行應用程式。

1. 從 Visual Studio 內部執行 **Receiver** 專案，然後等它啟動所有資料分割的接收器。
   
   ![][21]
2. 執行 **Sender** 程式，並查看接收器視窗中出現的事件。
   
   ![][24]

## <a name="next-steps"></a>後續步驟
您已經建置工作應用程式，可建立「事件中樞」和傳送及接收資料，接下來可進行下列案例：

* [使用事件中樞的完整範例應用程式][sample application that uses Event Hubs]。
* [使用事件中樞相應放大事件處理][Scale out Event Processing with Event Hubs]範例。
* [事件中樞概觀][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO1-->


