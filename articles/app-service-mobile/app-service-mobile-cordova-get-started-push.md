<properties
	pageTitle="使用 Azure Mobile Apps 新增推播通知至 Apache Cordova 應用程式| Azure App Service"
	description="了解如何使用 Azure Mobile Apps 將推播通知傳送至 Apache Cordova 應用程式。"
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="glenga"/>

# 新增推播通知至您的 Apache Cordova 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Overview

在本教學課程中，您會將推播通知新增至 [Apache Cordova 快速入門]專案，以便在每次插入一筆記錄時傳送推播通知。本教學課程以 [Apache Cordova 快速入門]教學課程為基礎，您必須先完成該教學課程。如果您有 ASP.NET 後端，且不使用下載的快速入門伺服器專案，您必須將推播通知擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure Mobile Apps 的 .NET 後端伺服器 SDK]。

##<a name="prerequisites"></a>必要條件

本教學課程涵蓋在 Visual Studio 2015 內開發且在 Google Android 模擬器、Android 裝置、Windows 裝置和 iOS 裝置上執行的 Apache Cordova 應用程式。

若要完成此教學課程，您需要下列項目：

* 具有 [Visual Studio Community 2015] 或更新版本的電腦。
* [Visual Studio Tools for Apache Cordova]。
* 有效的 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 已完成的 [Apache Cordova 快速入門]專案。可以先完成其他教學課程 (例如，[驗證])，但並非必要。
* (Android) 具有已驗證電子郵件地址和 Android 裝置的 [Google 帳戶]。
* (iOS) Apple Developer Program 成員資格和 iOS 裝置 (iOS 模擬器不支援推播)
* (Windows) Windows 市集開發人員帳戶和 Windows 10 裝置

雖然 Android 模擬器支援推播通知，但是我們發現它們很不穩定，因此不建議您在模擬器上測試推播通知。

##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

[觀看示範類似步驟的影片](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>修改 Cordova 應用程式以接收推播通知

您必須確認自己的 Apache Cordova 應用程式專案已經能夠處理推播通知。您必須安裝 Cordova 推播外掛程式，再加上任何平台特定的推播服務。

#### 更新您的專案中的 Cordova 版本。

如果您的專案使用舊版進行設定，建議您將用戶端專案更新為 Cordova 6.1.1。若要更新專案，請以滑鼠右鍵按一下 config.xml 來開啟組態設計工具。選取 [平台] 索引標籤，然後在 [Cordova CLI] 文字方塊中選擇 6.1.1。

依序選擇 [建置] 和 [建置方案] 來更新專案。

#### 安裝推播外掛程式

Apache Cordova 應用程式原本就不會處理裝置或網路功能。這些功能是由 [npm](https://www.npmjs.com/) 或 GitHub 上發佈的外掛程式所提供。`phonegap-plugin-push` 外掛程式是用來處理網路推播通知。

您可透過下列其中一種方式安裝推撥外掛程式：

**從命令提示：**

執行以下命令：

    cordova plugin add phonegap-plugin-push

**從 Visual Studio 內：**

1.  在方案總管中開啟 `config.xml` 檔案，按一下 [外掛程式] > [自訂]，選取 [Git] 作為安裝來源，然後輸入 `https://github.com/phonegap/phonegap-plugin-push` 作為來源。

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  按一下安裝來源旁邊的箭頭。

3. 在 **SENDER\_ID** 中，如果您已經有 Google 開發人員主控台專案的數值專案識別碼，您可以在此將它加入。否則，請輸入預留位置值，例如 777777，而如果您是以 Android 為目標，您可以稍後在 config.xml 中更新此值。

4. 按一下 [新增]。

推撥外掛程式現已安裝。

####安裝裝置外掛程式

遵循您用來安裝推播外掛程式的相同程序，但您會在核心外掛程式清單中找到裝置外掛程式 (按一下 [外掛程式] > [核心] 即可找到它)。您需要此外掛程式才能取得平台名稱 (`device.platform`)。

#### 註冊您的裝置在啟動時推播

一開始，我們會包含一些適用於 Android 的最低限度程式碼。稍後，我們將會進行一些小幅修改以在 iOS 或 Windows 10 上執行。

1. 在登入程序的回呼期間或是在 **onDeviceReady** 方法的底部，新增對 **registerForPushNotifications** 的呼叫：

		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');

		        // Refresh the todoItems
		        refreshDisplay();

		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);

				    // Added to register for push notifications.
		        registerForPushNotifications();

		    }, handleError);

	此範例為驗證成功後呼叫 **registerForPushNotifications**，如果應用程式中同時使用推播通知和驗證，建議採此方法。

2. 加入新的 **registerForPushNotifications** 方法，如下所示︰

		// Register for Push Notifications. Requires that phonegap-plugin-push be installed.
		var pushRegistration = null;
		function registerForPushNotifications() {
		  pushRegistration = PushNotification.init({
		      android: { senderID: 'Your_Project_ID' },
		      ios: { alert: 'true', badge: 'true', sound: 'true' },
		      wns: {}
		  });

		// Handle the registration event.
		pushRegistration.on('registration', function (data) {
		  // Get the native platform of the device.
		  var platform = device.platform;
		  // Get the handle returned during registration.
		  var handle = data.registrationId;
		  // Set the device-specific message template.
		  if (platform == 'android' || platform == 'Android') {
		      // Register for GCM notifications.
		      client.push.register('gcm', handle, {
		          mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'iOS') {
		      // Register for notifications.            
		      client.push.register('apns', handle, {
		          mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'windows') {
		      // Register for WNS notifications.
		      client.push.register('wns', handle, {
		          myTemplate: {
		              body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
		              headers: { 'X-WNS-Type': 'wns/toast' } }
		      });
		  }
		});

		pushRegistration.on('notification', function (data, d2) {
		  alert('Push Received: ' + data.message);
		});

		pushRegistration.on('error', handleError);
		}

3. (Android) 在上述程式碼中，從 [Google Developer Console] 使用應用程式的數字專案識別碼取代 `Your_Project_ID`。

## (選擇性) 在 Android 上設定和執行應用程式

完成本節可以為 Android 啟用推播通知。

####<a name="enable-gcm"></a>啟用 Google Cloud Messaging

因為我們一開始是以 Google Android 平台為目標，所以您必須啟用 Google 雲端通訊。同樣地，如果您以 Microsoft Windows 裝置為目標，您應該啟用 WNS 支援。

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

[觀看示範類似步驟的影片](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-4-Set-up-gcm-for-push)

####<a name="configure-backend"></a>設定行動應用程式後端以使用 GCM 傳送推送要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####針對 Android 設定您的 Cordova 應用程式

請在 Cordova 應用程式中，從 [Google Developer Console] 開啟 config.xml 並使用您應用程式的數字專案識別碼取代 `Your_Project_ID`。

		<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
			<variable name="SENDER_ID" value="Your_Project_ID" />
		</plugin>

開啟 index.js，並更新程式碼以使用您的數值專案識別碼。

		pushRegistration = PushNotification.init({
			android: { senderID: 'Your_Project_ID' },
			ios: { alert: 'true', badge: 'true', sound: 'true' },
			wns: {}
		});

####<a name="configure-device"></a>設定 Android 裝置進行 USB 偵錯

在您可以將應用程式部署到您的 Android 裝置之前，您需要啟用 USB 偵錯。在您的 Android 手機上執行下列步驟：

1. 移至 [設定] > [關於手機]，然後點選 [版本號碼]，直到啟用開發人員模式為止 (大約 7 次)。

2. 回到 [設定] > [開發人員選項] 啟用 [USB 偵錯]，然後使用 USB 纜線將 Android 手機連接到開發電腦。

我們測試時使用的是執行 Android 6.0 (Marshmallow) 的 Google Nexus 5 X 裝置。不過，這些技術在任何現代化 Android 版本中都是相同的。

#### 安裝 Google Play 服務

推播外掛程式仰賴 Android Google Play 服務來進行推播通知。

1.  在 [Visual Studio] 中按一下 [工具] > [Android] > [Android SDK Manager]，然後展開 [Extras] 資料夾並核取方塊，確定已安裝下列所有的 SDK。
    * Android Support Repository (版本 20 或更高版本)
    * Google Play 服務 (版本 27 或更高版本)
    * Google Repository (版本 22 或更高版本)

2.  按一下 [Install Packages] \(安裝封裝)，並等候安裝完成。

目前的必要程式庫會在 [phonegap-plugin-push Installation 文件]中列出。

#### 在 Android 上的應用程式中測試推播通知

您現在可以執行應用程式，在 TodoItem 資料表中插入項目，以測試推播通知。只要使用相同的後端，您可以在相同的裝置或第二部裝置上執行這項作業。以下列方法之一在 Android 平台上測試 Cordova 應用程式︰

- **實體裝置︰**使用 USB 纜線將 Android 裝置連接到開發電腦。請選取 [裝置]，不要選取 [Google Android 模擬器]。Visual Studio 會將應用程式部署至裝置並執行。您接著可以在裝置上與應用程式互動。改善您的開發經驗。畫面共用應用程式 (例如 [Mobizen]) 可在電腦上將您的 Android 畫面投射到 Web 瀏覽器，並協助您開發 Android 應用程式。

- **在 Android 模擬器上︰**在模擬器上執行時，您需要採取其他設定步驟。

	針對您要部署的目的地虛擬裝置或偵錯的所在虛擬裝置，請確認該裝置具有已設定為目標的 Google API，如以下 Android 虛擬裝置管理員 (AVD) 所示。

	![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

	如果您想要使用更快速的 x86 模擬器，請[安裝 HAXM 驅動程式](https://taco.visualstudio.com/zh-TW/docs/run-app-apache/#HAXM)並設定模擬器使用它。

	按一下 [應用程式] > [設定] > [加入帳戶]，將 Google 帳戶加入至 Android 裝置，然後依照提示將現有的 Google 帳戶加入至此裝置 (建議使用現有的帳戶，而非建立新帳戶)。

	![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

	按照先前的方法執行 todolist 應用程式，然後插入新的 todo 項目。這次，通知圖示會顯示在通知區域中。您可以開啟通知抽屜來檢視通知的完整內容。

	![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##(選擇性) 在 iOS 上設定和執行

本節適用於在 iOS 裝置上執行 Cordova 專案。如果未使用 iOS 裝置，可以略過這一節。

####在 Mac 或雲端服務上安裝及執行 iOS remotebuild 代理程式

請先進行 [iOS 安裝指南](http://taco.visualstudio.com/zh-TW/docs/ios-guide/)中的步驟來安裝和執行 remotebuild 代理程式，才可以使用 Visual Studio 在 iOS 上執行 Cordova 應用程式。

確定您可以建置適用於 iOS 的應用程式。必須執行安裝指南中的步驟才能從 Visual Studio 針對 iOS 建置。如果您沒有 Mac，您可以在 MacInCloud 之類的服務上使用 remotebuild 代理程式針對 iOS 建置。如需詳細資訊，請參閱[在雲端中執行 iOS 應用程式](http://taco.visualstudio.com/zh-TW/docs/build_ios_cloud/)。

>[AZURE.NOTE] 必須有 XCode 7 或更新版本，才能在 iOS 上使用推播外掛程式。

####尋找要做為應用程式識別碼的識別碼

在針對推播通知註冊您的應用程式之前，在 Cordova 應用程式中開啟 config.xml，在 widget 元素中尋找 `id` 屬性值，並加以複製以供稍後使用。在下列 XML 中，識別碼為 `io.cordova.myapp7777777`。

		<widget defaultlocale="zh-TW" id="io.cordova.myapp7777777"
  		version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
			xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

稍後，當您在 Apple 開發人員入口網站上建立應用程式識別碼時，請使用這個識別碼。(如果您在開發人員入口網站上建立不同的應用程式識別碼而且想要使用該識別碼，您稍後必須在本教學課程中採取一些額外的步驟，以在 config.xml 中變更此識別碼。widget 元素中的識別碼必須相符開發人員入口網站上的應用程式識別碼)。

####在 Apple 的開發人員入口網站註冊應用程式以取得推播通知

[AZURE.INCLUDE [通知中樞 Xamarin 啟用 Apple 推播通知](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

[觀看示範類似步驟的影片](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####設定 Azure 來傳送推播通知

1. 登入 [Azure 入口網站](https://portal.azure.com/)。按一下 [瀏覽] > [Mobile Apps] > 您的行動應用程式 > [設定] > [推送] > [Apple (APNS)] > [上傳憑證]。上傳您稍早匯出的 .p12 推播憑證檔案。如果您建立開發推播憑證是為了開發和測試，請務必選取 [沙箱]。否則，請選擇 [生產]。您的服務現在已設定為對 iOS 使用推播通知。

	![](./media/app-service-mobile-cordova-get-started-push/mobile-app-upload-apns-cert.png)

####確認您的應用程式識別碼符合 Cordova 應用程式

如果您在 Apple 開發人員帳戶中建立的應用程式識別碼已經符合 config.xml 中 widget 元素的識別碼，您即可略過此步驟。不過，如果識別碼不相符，請採取下列步驟︰

1. 從您的專案中刪除 platforms 資料夾。

2. 從您的專案中刪除 plugins 資料夾。

3. 從您的專案中刪除 node\_modules 資料夾。

4. 更新 config.xml 中的 widget 元素的 id 屬性，才能使用您在 Apple 開發人員帳戶中建立的應用程式識別碼。

5. 重建您的專案。

#####在 iOS 應用程式中測試推播通知

1. 在 Visual Studio 中，確定已選取 **iOS** 做為部署目標，然後選擇 [裝置] 以在連線的 iOS 裝置上執行。

	您可以在使用 iTunes 連線至您的 PC 的 iOS 裝置上執行。iOS 模擬器不支援推播通知。

2. 在 Visual Studio 中按下 [執行] 按鈕或 **F5** 以建置專案，並在 iOS 裝置上啟動應用程式，然後按一下 [確定] 以接受推播通知。

	>[AZURE.NOTE] 您必須明確地接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

3. 在應用程式中輸入一項工作，然後按一下加號 (+) 圖示。

4. 確認您已接收到通知，然後按一下 [確定] 以關閉通知。

##(選擇性) 在 Windows 上設定和執行

本節適用於在 Windows 10 裝置 (Windows 10 支援 PhoneGap 推播外掛程式) 上執行 Apache Cordova 應用程式專案。如果未使用 Windows 裝置，可以略過這一節。

####向 WNS 註冊 Windows 應用程式以使用推播通知

若要使用 Visual Studio 中的 [存放區] 選項，請從 [方案平台] 清單中選取 Windows 目標，例如 **Windows-x64** 或 **Windows-x86** (避免 **Windows-AnyCPU** 使用推播通知)。

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[觀看示範類似步驟的影片](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####設定 WNS 的通知中樞

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####設定 Cordova 應用程式以支援 Windows 推播通知

開啟組態設計工具 (以滑鼠右鍵按一下 config.xml 並選取 [檢視表設計工具])，選取 [Windows] 索引標籤，然後選擇 [Windows 目標版本] 下的 [Windows 10]。

>[AZURE.NOTE] 如果您使用 Cordova 5.1.1 之前的 Cordova 版本 (建議使用 6.1.1)，您也必須在 config.xml 中將 [支援快顯通知] 旗標設定為 true。

若要在您的預設 (偵錯) 組建中支援推播通知，請開啟 build.json 檔案。將 "release" 組態複製到您的偵錯組態。

		"windows": {
			"release": {
				"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
				"publisherId": "CN=yourpublisherID"
			}
		}

更新之後，上述程式碼應如下所示。

	"windows": {
		"release": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			},
		"debug": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			}
		}

建置應用程式並確認沒有錯誤。您的用戶端應用程式現在應該註冊行動應用程式後端的通知。針對方案中每個 Windows 專案重複操作這一節。

####在 Windows 應用程式中測試推播通知

在 Visual Studio 中，確定已選取 Windows 平台做為部署目標，例如 **Windows-x64** 或 **Windows-x86**。若要在裝載 Visual Studio 的 Windows 10 電腦上執行應用程式，請選擇 [本機電腦]。

按 [執行] 按鈕，以建置專案並啟動應用程式。

在應用程式中輸入新 todoitem 的名稱，然後按一下加號 (+) 圖示來加入它。

確認在加入項目時收到通知。

##<a name="next-steps"></a>後續步驟

* 請閱讀[通知中樞]，了解推播通知的相關資訊。
* 如果您尚未這麼做，請[新增驗證]至您的 Apache Cordova 應用程式，繼續教學課程。

了解如何使用 SDK。

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs -->
[新增驗證]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova 快速入門]: app-service-mobile-cordova-get-started.md
[驗證]: app-service-mobile-cordova-get-started-users.md
[使用 Azure Mobile Apps 的 .NET 後端伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google 帳戶]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google Developer Console]: https://console.developers.google.com/home/dashboard
[phonegap-plugin-push Installation 文件]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/zh-TW/features/cordova-vs.aspx
[通知中樞]: ../notification-hubs/notification-hubs-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0824_2016-->