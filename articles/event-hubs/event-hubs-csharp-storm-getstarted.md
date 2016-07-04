<properties
	pageTitle="搭配 Apache Storm 開始使用以 C# 撰寫的事件中樞 | Microsoft Azure"
	description="遵循此教學課程，以開始使用 Azure 事件中樞；以 C# 傳送事件並在 Apache Storm 叢集中接收這些事件。"
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/13/2016"
	ms.author="sethm"/>

# 開始使用事件中心

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## 簡介

事件中心是可高度擴充的擷取系統，每秒可以吸收數以百萬計的事件，讓應用程式能處理和分析已連線裝置和應用程式所產生的大量資料。收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集轉換和儲存資料。

如需詳細資訊，請參閱[事件中樞概觀]。

在本教學課程中，您將學習如何使用以 C# 撰寫的主控台應用程式將訊息擷取到事件中心，以及使用 Apache Storm 平行擷取它們。

若要完成本教學課程，您需要下列項目：

+ [Microsoft Visual Studio](http://visualstudio.com)

+ 為了執行 [Maven](http://maven.apache.org/) 所設定的 Java 開發環境。在本教學課程中，我們將採用 [Eclipse](https://www.eclipse.org/)

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.	從 Eclipse 執行 **LogTopology** 類別，然後等它啟動所有資料分割的接收器。

2.	執行 **Sender** 專案，按下主控台視窗中的 **Enter** 鍵，並查看出現在接收器視窗中的事件。

	![][22]

## 後續步驟

您已經建置工作應用程式，可建立「事件中樞」和傳送及接收資料，接下來可進行下列案例：

- [使用事件中樞的完整範例應用程式][]。
- [使用「事件中樞」相應放大事件處理][]範例。
- 使用服務匯流排佇列的[佇列訊息解決方案][]。

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[事件中樞概觀]: event-hubs-overview.md
[使用事件中樞的完整範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[使用「事件中樞」相應放大事件處理]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[佇列訊息解決方案]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0622_2016-->