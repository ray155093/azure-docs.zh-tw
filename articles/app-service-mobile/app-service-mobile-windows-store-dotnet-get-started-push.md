<properties
	pageTitle="將推播通知新增至您的通用 Windows 平台 (UWP) 應用程式 | Azure Mobile Apps"
	description="了解如何使用 Azure App Service Mobile Apps 與 Azure 通知中樞，將推播通知傳送至通用 Windows 平台 (UWP) 應用程式。"
	services="app-service\mobile,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/25/2016"
	ms.author="glenga"/>

# 將推播通知加入至 Windows 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Overview

本主題說明如何使用 Azure App Service 中的 Mobile Apps 與 Azure 通知中樞，將推播通知傳送至通用 Windows 平台 (UWP) 應用程式。在此案例中，加入新項目時，您的行動應用程式後端會傳送通知至所有註冊 Windows 通知服務 (WNS) 的 Windows 應用程式

本教學課程以 Azure Mobile Apps 快速入門為基礎。在開始本教學課程之前，您必須先完成快速入門教學課程：[建立 Windows 應用程式](app-service-mobile-windows-store-dotnet-get-started.md)。如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##針對推播通知註冊應用程式

在您能從 Azure 傳送推播通知到您的 Windows 應用程式之前，您必須將應用程式提交至 Windows 市集。接著您可以設定您的伺服器專案與 WNS 整合。

1. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下 UWP 應用程式專案，然後按一下 [市集] > [將應用程式與市集建立關聯...]。

    ![建立應用程式與 Windows 市集的關聯](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. 在精靈中按 [**下一步**]、使用 Microsoft 帳戶登入，在 [**保留新的應用程式名稱**] 中輸入您應用程式的名稱，然後按一下 [**保留**]。

3. 成功建立應用程式註冊之後，選取新的應用程式名稱，按一下 [下一步]，然後按一下 [關聯]。這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

7. 瀏覽至 [Windows 開發人員中心](https://dev.windows.com/zh-TW/overview)、使用您的 Microsoft 帳戶登入、在 [**我的應用程式**] 中按一下 [新增應用程式註冊]，然後展開 [**服務**] > [**推播通知**]。

8. 在 [**推播通知**] 頁面上，按一下 [**Microsoft Azure 行動服務**] 底下的 [**線上服務網站**]。

9. 在註冊頁面中，記下 [應用程式密碼] 和 [封裝 SID] 底下的值，以在接下來用來設定您的行動應用程式後端。

	![建立應用程式與 Windows 市集的關聯](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] 用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些值，或與您的應用程式一起散發密碼。**應用程式識別碼**會與密碼搭配用來設定 Microsoft 帳戶驗證。

##設定後端來傳送推播通知

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>更新伺服器以傳送推播通知

現在應用程式已經啟用了推播通知，您必須更新應用程式後端以傳送推播通知。使用下列符合您後端專案類型的程序 &mdash; [.NET 後端](#dotnet)或 [Node.js 後端](#nodejs)。

### <a name="dotnet"></a>.NET 後端專案

1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [管理 NuGet 封裝]，搜尋 Microsoft.Azure.NotificationHubs，然後按一下 [安裝]。這會安裝通知中樞用戶端程式庫。

2. 展開 [Controllers]，開啟 [TodoItemController.cs]，然後新增下列 using 陳述式：

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. 在 **PostTodoItem** 方法中，於呼叫 **InsertAsync** 之後新增下列程式碼：

	    // Get the settings for the server project.
	    HttpConfiguration config = this.Configuration;
	    MobileAppSettingsDictionary settings =
	        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

	    // Get the Notification Hubs credentials for the Mobile App.
	    string notificationHubName = settings.NotificationHubName;
	    string notificationHubConnection = settings
	        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

	    // Create the notification hub client.
	    NotificationHubClient hub = NotificationHubClient
	        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

	    // Define a WNS payload
	    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
	                            + item.Text + @"</text></binding></visual></toast>";
	    try
	    {
	        // Send the push notification.
	        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

	        // Write the success result to the logs.
	        config.Services.GetTraceWriter().Info(result.State.ToString());
	    }
	    catch (System.Exception ex)
	    {
	        // Write the failure result to the logs.
	        config.Services.GetTraceWriter()
	            .Error(ex.Message, null, "Push.SendAsync Error");
	    }

	此程式碼會告訴通知中樞在插入新項目之後傳送推播通知。

4. 發佈伺服器專案。

### <a name="nodejs"></a>Node.js 後端專案

1. 如果您還沒這麼做，請下載[快速入門專案](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)或使用 [Azure 入口網站中的線上編輯器](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。

2. 在 todoitem.js 檔案中，以下列程式碼取代現有的程式碼：

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');

		var table = azureMobileApps.table();

		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK,
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');

	    // Define the WNS payload that contains the new item Text.
	    var payload = "<toast><visual><binding template=\ToastText01><text id="1">"
		                            + context.item.text + "</text></binding></visual></toast>";

	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a WNS native toast notification.
	                context.push.wns.sendToast(null, payload, function (error) {
	                    if (error) {
	                        logger.error('Error while sending push notification: ', error);
	                    } else {
	                        logger.info('Push notification sent successfully!');
	                    }
	                });
	            }
	            // Don't forget to return the results from the context.execute()
	            return results;
	        })
	        .catch(function (error) {
	            logger.error('Error while running context.execute: ', error);
	        });
		});

		module.exports = table;

	插入新的 todo 項目時，這會傳送包含 item.text 的 WNS 快顯通知。

2. 當您在本機電腦上編輯檔案時，請重新發佈伺服器專案。

##<a id="update-app"></a>將推播通知新增至應用程式

接下來，您的應用程式必須在啟動時註冊推播通知。當您已啟用驗證時，請確定使用者在嘗試註冊推播通知之前已登入。如需詳細資訊，請參閱快速入門完整範例中的[第一次驗證](https://github.com/Azure-Samples/app-service-mobile-windows-quickstart/blob/master/README.md#authenticate-first)。

1. 開啟 **App.xaml.cs** 專案檔案，並新增下列 `using` 陳述式：

		using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. 在相同檔案中，將下列 **InitNotificationsAsync** 方法定義新增至 [**應用程式**] 類別：

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    此程式碼會從 WNS 中擷取應用程式的 ChannelURI，然後向您的應用程式服務行動應用程式註冊該 ChannelURI。

3. 在 **App.xaml.cs** 中 **OnLaunched** 事件處理常式的頂端，將 **async** 修飾詞新增到方法定義中，並將下列呼叫新增到新的 **InitNotificationsAsync** 方法中，如下列範例所示：

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    這樣可保證在每次啟動應用程式時都會註冊存留期較短的 ChannelURI。

4. 重建 UWP 應用程式專案。您的應用程式現在已能夠接收快顯通知。

##<a id="test"></a>在應用程式中測試推播通知

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>接續步驟

進一步了解推播通知︰

* [如何針對 Azure Mobile Apps 使用受管理的用戶端](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)：範本可讓您彈性地傳送跨平台推播和當地語系化推播。了解如何註冊範本。

* [使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags)：標記可讓您使用推播鎖定區隔的客戶。了解如何將標記加入至裝置安裝。

* [診斷推播通知問題](../notification-hubs/notification-hubs-push-notification-fixer.md)：通知遭到捨棄或未抵達裝置有各種原因。本主題說明如何分析及找出推播通知失敗的根本原因。

請考慮繼續進行下列其中一個教學課程：

+ [將驗證加入應用程式中](app-service-mobile-windows-store-dotnet-get-started-users.md)：了解如何使用識別提供者來驗證應用程式的使用者。

+ [啟用應用程式的離線同步處理](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)：了解如何使用行動應用程式後端，將離線支援加入至應用程式。離線同步處理可讓使用者與行動應用程式進行互動 - 檢視、新增或修改資料 - 即使沒有網路連線也可行。

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_0928_2016-->