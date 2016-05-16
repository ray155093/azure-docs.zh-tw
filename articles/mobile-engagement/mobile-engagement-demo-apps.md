<properties
	pageTitle="Azure Mobile Engagement 示範應用程式"
	description="描述由何處下載、如何使用以及尋找使用 Azure Mobile Engagement 示範應用程式的優點"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="piyushjo" />

# Azure Mobile Engagement 示範應用程式

我們已針對 **iOS**、**Android** 和 **Windows** 平台發行 Azure Mobile Engagement 示範應用程式，協助您找到有用的資源，並深入了解 Azure Mobile Engagement。

應用程式可協助您︰

1. 輕鬆地找到有用的 Azure Mobile Engagement 特定資源連結，例如影片、文件、支援論壇、可在何處提出功能要求等等。 
2. 體驗 Azure Mobile Engagement 支援的範例通知，以獲得自己的行動應用程式的想法。 
3. 提供參考實作，您可以用來研究如何將 Mobile Engagement 實作到您自己的應用程式中︰ 

	- 收集分析資料 
	- 實作例如「全螢幕插入」或「快顯」等類型的進階通知案例
	- 實作問卷/投票
	- 實作無訊息推播/資料推播案例   

## 應用程式安裝
此應用程式提供於個別的應用程式市集中︰

1. **Windows Universal 示範應用程式**

	- [Windows 應用程式市集的下載連結](https://www.microsoft.com/zh-TW/store/apps/azure-mobile-engagement/9nblggh4qmh2) 
	- 應用程式是開發為 Windows 10 Universal 應用程式，原始程式碼位於 [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

2. **iOS 示範應用程式**

	- [Apple Store 的下載連結](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090) 
	- 應用程式以 iOS Swift 開發，原始程式碼位於 [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

3. **Android 示範應用程式**

	- [Google Play Store 的下載連結](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)
	- 原始程式碼位於 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android)

![][1]

![][2] ![][3]


## 使用量

您可以透過下列方式使用這些應用程式︰

**1) 從上面提供的應用程式市集連結，在您的裝置上下載應用程式。**

>[AZURE.IMPORTANT] 您不需要任何 Azure 帳戶，也不需要將應用程式連接至任何後端。應用程式會獨立運作。

- 一旦您的裝置上已有應用程式，就可以在左側功能表中瀏覽連結，尋找有關 Azure Mobile Engagement 的所有有用資源。 
- 我們也將我們的[服務 RSS 摘要](https://aka.ms/azmerssfeed)加入此應用程式中，因此您隨時都可以得知最新的產品更新。
- 您也可以瀏覽範例通知案例，體驗 Azure Mobile Engagement 針對每個平台支援哪些類型的通知。這些通知可以在本機體驗，也就是您可以按一下畫面上的按鈕，顯示與您從 Azure Mobile Engagement 平台傳送通知時相同的通知體驗。 

![][4]
    
![][5] ![][6]

**2) 從上面提供的 Github 連結下載原始程式碼。**

- 一旦您已下載原始程式碼，請在個別的開發環境中開啟它：適用於 iOS 的 XCode、適用於 Android 的 Android Studio、適用於 Windows 的 Visual Studio。 
- 接下來您應該遵循我們的[基本 SDK 整合步驟](mobile-engagement-windows-store-dotnet-get-started.md)，以便能夠將此應用程式連接到它自己的 Mobile Engagement 後端執行個體。 
	- 您必須在應用程式中設定連接字串。  
	- 您也必須設定應用程式的推播通知平台。 
- 您會注意到此應用程式本身使用 Azure Mobile Engagement 檢測，因此當您在將它連接到後端之後開啟應用程式時，將能在 [監視] 索引標籤上看到使用者工作階段、活動、事件等。 
- 您也可以從自己的 Mobile Engagement 執行個體傳送通知給此應用程式，而不是使用本機通知。 
	- 這裡使用應用程式的 [Get the Device ID] (取得裝置識別碼) 功能表項目將裝置加入作為測試裝置，它會提供您裝置識別碼，接著您會向您的平台後端執行個體註冊為測試裝置。 

	![][7]
	    
	![][8] ![][9]

## 示範應用程式的主要功能

1. 如前所述，使用此應用程式，您已經掌握了 Azure Mobile Engagement 的所有重要資源。您可以瀏覽左側功能表中的連結。 

2. 體驗每種平台的應用程式外通知。這些通知可以傳遞作為**僅通知**，按一下通知將只會開啟應用程式的原生螢幕 (使用**深層連結**)，或是作為**網頁公告**，您可以從 Mobile Engagement 後端傳遞要在按一下通知時顯示的其他 HTML 內容。

	![][29]

	
	- 在 iOS 上，您必須關閉應用程式，才能看到應用程式外通知或系統推播通知。您可以查看此處的實作，加入**動作按鈕**，就像加入此處應用程式外通知的「意見反應」和「共用」按鈕，讓使用者可以直接從通知本身採取動作。 
	    
	![][11] ![][14]
	
	
	- 在 Android 上，您會看到 Android 支援的選項，它們會以在通知中加入多行文字 (**大型文字**) 或在通知中加入影像 (**概略圖**) 的形式表示，也會看到如同 iOS 所支援的**動作按鈕**。 
	
	![][12] ![][15]
	
	
	- 在 Windows 10 中，您可以看到通知在電腦上顯示的外觀。此通知也會顯示在 Windows 10 **通知中心**。目前不支援使用 Windows SDK 加入**動作按鈕**。 
	
	![][10] ![][13]

3. 體驗每種平台的預設應用程式內通知。這是兩個步驟的體驗，當中會先顯示 [通知] 視窗，按一下它時會開啟了全螢幕的**宣告**，如下所示。此宣告的內容來自您的 Mobile Engagement 後端執行個體。SDK 有適用於通知和宣告的範本，可以像在此示範應用程式中一樣輕鬆地自訂，並且加上我們的標誌和色彩。

	![][16]
	
	![][17] **iOS** ![][18] **Android**

4. 您也可以使用 Azure Mobile Engagement 的 [類別] 功能來自訂此預設體驗。在示範應用程式中，我們已示範了兩種常見的方式來變更通知的體驗。請注意，在 Windows SDK 中尚未支援類別功能。

	**全螢幕插入**
	
	![][30]

	![][21] ![][22]

	**快顯通知**

	![][31]
	
	![][19] ![][20]

5. Azure Mobile Engagement 也支援特殊類型的應用程式內通知，稱為**投票**，可讓您傳送快速問卷調查給您的區隔應用程式使用者。您可以加入問題和每個問題的選項，如下所示，然後作為應用程式內通知顯示給應用程式使用者。

	![][32]

	![][26]
	    
	![][27] **iOS** ![][28] **Android**

5. Azure Mobile Engagement 也支援傳送無訊息的**資料推播**通知，您可以從您的服務傳送一些資料，像是下列範例中的 JSON 資料，以便在應用程式中處理並採取某種動作。比方說，我們如何選擇性地使用資料推播通知改變項目的價格。

	![][33]

	![][23]
	    
	![][24] **iOS** ![][25] **Android**

> [AZURE.NOTE] 請注意，您可以按一下任何範例通知螢幕上的[Click here for instructions on how to send these notifications from Mobile Engagement platform] (如需如何從 Mobile Engagement 平台傳送這些通知的指示，請按一下這裡)，檢視任何這些通知的詳細逐步指示 。


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png

<!---HONumber=AcomDC_0504_2016-->