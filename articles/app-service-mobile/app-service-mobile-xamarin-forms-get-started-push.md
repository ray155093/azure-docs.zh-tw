<properties
	pageTitle="將推播通知新增至 Xamarin.Forms 應用程式 | Microsoft Azure"
	description="了解如何使用 Azure 服務將多平台推播通知傳送至 Xamarin.Forms 應用程式。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/17/2016"
	ms.author="wesmc"/>

# 將推播通知新增至 Xamarin.Forms 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##概觀

本主題說明如何使用 Azure 服務，將推播通知傳送至各種原生裝置平台 (Android、iOS 和 Windows) 上執行的 Xamarin.Forms 應用程式。Azure Mobile Apps 後端會使用 Azure 通知中樞傳來推播通知。由於使用範本註冊，相同的訊息可以透過各種推播通知服務 (PNS)，傳送至所有平台上執行的裝置。如需傳送跨平台推播通知的詳細資訊，請參閱 [Azure 通知中樞](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)文件。

您可以將推播通知新增至 Xamarin.Forms 應用程式支援的每個專案。後端每次插入記錄時，就會傳送推播通知。

##必要條件

為了讓本教學課程產生最佳結果，建議您先完成[建立 Xamarin.Forms 應用程式](app-service-mobile-xamarin-forms-get-started.md)教學課程。完成本教學課程之後，您將會有一個多平台 TodoList 應用程式的 Xamarin.Forms 專案。

如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

將推播通知傳送至 iOS 裝置需要具備 [Apple Developer Program 成員資格](https://developer.apple.com/programs/ios/)。此外，您必須使用實體 iOS 裝置，因為 [iOS 模擬器不支援推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。

##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(選擇性) 設定和執行 Android 專案

完成本節可以為適用於 Android 的 Xamarin.Forms Droid 專案啟用推播通知。


###啟用 Google 雲端通訊 (GCM)

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###設定行動應用程式後端以使用 GCM 傳送推送要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###將推播通知新增至 Android 專案

將後端設定為使用 Google 雲端通訊 (GCM) 後，我們可以將元件和程式碼新增至用戶端，使應用程式能夠向 GCM 註冊、透過行動應用程式後端向 Azure 通知中樞註冊推播通知，並且接收通知。

1. 在 **Droid** 專案中，以滑鼠右鍵按一下 [元件] 資料夾，按一下 [取得更多元件...]，搜尋 [Google 雲端通訊用戶端] 元件，然後將它新增至專案。此元件支援 Xamarin Android 專案的推播通知。


2. 開啟 MainActivity.cs 專案檔案，然後將下列 using 陳述式加入至檔案頂端：

		using Gcm.Client;

3. 在 **OnCreate** 方法中，在呼叫 **LoadApplication** 之後加入下列程式碼：

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);

	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. 加入新的 **CreateAndShowDialog** 協助程式方法，如下所示︰

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. 將下列程式碼加入至 **MainActivity** 類別：

		// Create a new instance field for this activity.
		static MainActivity instance = null;

		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

	這會公開目前的 **MainActivity** 執行個體，讓我們可以在主要 UI 執行緒上執行。

6. 初始化 **OnCreate** 方法開頭的 `instance` 變數，如下所示。

		// Set the current instance of MainActivity.
		instance = this;

2. 在名為 `GcmService.cs` 的 **Droid** 專案中，加入新的類別檔案，並確定下列 **using** 陳述式出現在檔案頂端︰

		using Android.App;
		using Android.Content;
		using Android.Media;
		using Android.Support.V4.App;
		using Android.Util;
		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Text;


9. 在檔案頂端的 **using** 陳述式和 **namespace** 宣告之間，加入下列權限要求。

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. 將下列類別定義加入至命名空間。

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

	>[AZURE.NOTE]以您先前記下的專案號碼取代 **<PROJECT\_NUMBER>**。

11. 使用下列程式碼取代空的 **GcmService** 類別，以使用新的廣播接收器︰

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. 在 **GcmService** 類別中加入下列程式碼，以覆寫 **OnRegistered** 事件處理常式並實作 **Register** 方法。

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                	{"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

		Note that this code uses the `messageParam` parameter in the template registration. 

13. 加入下列程式碼以實作 **OnMessage**：

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

		    var msg = new StringBuilder();

		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }

		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();

		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }

		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }

		    createNotification("Unknown message details", msg.ToString());
		}

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

	這會處理內送通知，並將它們傳送至通知管理員來顯示。

14. **GcmServiceBase** 也會要求您實作 **OnUnRegistered** 和 **OnError** 處理常式方法，您的做法如下所示：

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}

現在，您已經準備好在 Android 裝置或模擬器上執行的應用程式中測試推播通知。

###在 Android 應用程式中測試推播通知

只有在模擬器上測試時，才需要前兩個步驟。

1. 針對您要部署的目的地虛擬裝置或偵錯的所在虛擬裝置，請確認該裝置具有已設定為目標的 Google API，如以下 Android 虛擬裝置管理員 (AVD) 所示。

2. 按一下 [應用程式] > [設定] > [加入帳戶] 將 Google 帳戶加入 Android 裝置，然後依照提示使用裝置現有的 Google 帳戶來建立新帳戶。

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下 **Droid** 專案，然後按一下 [設定為啟始專案]。

2. 按 [執行] 按鈕以建置專案，並在 Android 裝置或模擬器上啟動應用程式。

3. 在應用程式中輸入一項工作，然後按一下加號 (**+**) 圖示。

4. 確認在加入項目時收到通知。


##(選擇性) 設定和執行 iOS 專案

這一節適用於對 iOS 裝置執行 Xamarin iOS 專案。如果未使用 iOS 裝置，可以略過這一節。

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####設定 APNS 的通知中樞

1. 登入 [Azure 入口網站](https://portal.azure.com/)。按一下 [瀏覽] > [Mobile Apps] > 您的行動應用程式 > [設定] > [推送] > [Apple (APNS)] > [上傳憑證]。上傳您稍早匯出的 .p12 推播憑證檔案。如果您建立開發推播憑證是為了開發和測試，請務必選取 [沙箱]。否則，請選擇 [生產]。您的服務現在已設定為對 iOS 使用推播通知。

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	接下來，您將在 Xamarin Studio 或 Visual Studio 中設定 iOS 專案設定。

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####將推播通知加入至 iOS 應用程式

1. 在 **iOS** 專案中開啟 AppDelegate.cs，並將下列 **using** 陳述式新增到程式碼檔案頂端。

        using Newtonsoft.Json.Linq;

4. 在 **AppDelegate** 類別中，新增 **RegisteredForRemoteNotifications** 事件的覆寫以註冊通知：

        public override void RegisteredForRemoteNotifications(UIApplication application, 
			NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. 此外，也在 **AppDelegate** 中新增 **DidReceivedRemoteNotification** 事件處理常式的下列覆寫：

        public override void DidReceiveRemoteNotification(UIApplication application, 
			NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

	當應用程式執行時，此方法會處理傳入的通知。

2. 在 **AppDelegate** 類別中，將下列程式碼新增到 **FinishedLaunching** 方法：

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

	這能夠支援遠端通知，並要求推播註冊。

您的應用程式現在已更新為支援推播通知。

####在 iOS 應用程式中測試推播通知

1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [設定為啟始專案]。

2. 在 Visual Studio 中按下 [執行] 按鈕或 **F5** 以建置專案，並在 iOS 裝置上啟動應用程式，然後按一下 [確定] 以接受推播通知。

	> [AZURE.NOTE] 您必須明確地接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

3. 在應用程式中輸入一項工作，然後按一下加號 (**+**) 圖示。

4. 確認您已接收到通知，然後按一下 [確定] 以關閉通知。


##(選擇性) 設定和執行 Windows 專案

本節說明執行適用於 Windows 裝置的 Xamarin.Forms WinApp 和 WinPhone81 專案。這些步驟也支援通用 Windows 平台 (UWP) 專案。如果未使用 Windows 裝置，可以略過這一節。


####向 WNS 註冊 Windows 應用程式以使用推播通知

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####設定 WNS 的通知中樞

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####將推播通知加入至 Windows 應用程式

1. 在 Visual Studio 中，開啟 Windows 專案中的 **App.xaml.cs**，並新增下列 **using** 陳述式。

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using <your_TodoItemManager_portable_class_namespace>;

	使用包含 `TodoItemManager` 類別的可攜式專案命名空間來取代 `<your_TodoItemManager_portable_class_namespace>`。
 

2. 在 App.xaml.cs 中，新增下列 **InitNotificationsAsync** 方法：

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
				"<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
			{
				{"body", templateBodyWNS},
				{"headers", headers} // Needed for WNS.
			};

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
				.RegisterAsync(channel.Uri, templates);
        }

	這個方法會取得推播通知通道，並註冊範本以接收來自通知中樞的範本通知。支援 messageParam 的範本通知會傳送到此用戶端。

3. 在 App.xaml.cs 中，新增 `async` 修飾詞以更新 **OnLaunched** 事件處理常式方法定義，然後在方法結尾處新增下一行程式碼：

        await InitNotificationsAsync();

	如此可確保每次啟動應用程式時都會建立或重新整理推播通知註冊。必須如此以保證 WNS 推送通道永遠在作用中。

4. 在 Visual Studio 的 [方案總管] 中，開啟 **Package.appxmanifest** 檔案，然後把 [通知] 下方的 [支援快顯通知] 設為 [是]。

5. 建置應用程式並確認沒有錯誤。您用戶端應用程式現在應該註冊行動應用程式後端的範本通知。針對方案中每個 Windows 專案重複操作這一節。


####在 Windows 應用程式中測試推播通知

1. 在 Visual Studio 中，以滑鼠右鍵按一下 Windows 專案，然後按一下 [設定為啟始專案]。

2. 按 [執行] 按鈕，以建立專案並啟動應用程式。

3. 在應用程式中輸入新 todoitem 的名稱，然後按一下加號 (**+**) 圖示來加入它。

4. 確認在加入項目時收到通知。

##後續步驟

進一步了解推播通知︰

* [使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) 標記可讓您使用推播鎖定區隔的客戶。了解如何將標記加入至裝置安裝。

* [診斷推播通知問題](../notification-hubs/notification-hubs-push-notification-fixer.md) 通知遭到捨棄或未抵達裝置有各種原因。本主題說明如何分析及找出推播通知失敗的根本原因。

請考慮繼續進行下列其中一個教學課程：

* [在您的應用程式中新增驗證](app-service-mobile-xamarin-forms-get-started-users.md) 了解如何使用身分識別提供者驗證應用程式的使用者。

* [啟用應用程式的離線同步處理](app-service-mobile-xamarin-forms-get-started-offline-data.md) 了解如何使用行動應用程式後端，將離線支援新增至應用程式。離線同步處理可讓使用者與行動應用程式進行互動 - 檢視、新增或修改資料 - 即使沒有網路連線也可行。

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0720_2016-->