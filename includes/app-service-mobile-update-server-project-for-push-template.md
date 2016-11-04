在本節中，您會更新現有 Mobile Apps 後端專案中的程式碼，以在每次新增新項目時傳送推播通知。因為用戶端已使用範本註冊註冊以取得推播通知，所以單一推播通知訊息可以傳送給所有用戶端平台。每個用戶端範本註冊都包含一個 *messageParam* 參數"。傳送通知時，*messageParam* 會包含一個字串，該字串為所插入項目的文字。如需搭配通知中樞使用範本的詳細資訊，請參閱[範本](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。

選擇下列符合您後端專案類型的程序 &mdash; [.NET 後端](#dotnet)或 [Node.js 後端](#nodejs)。

### <a name="dotnet"></a>.NET 後端專案
1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [管理 NuGet 封裝]，搜尋 `Microsoft.Azure.NotificationHubs`，然後按一下 [安裝]。這會安裝通知中樞程式庫，以便從後端傳送通知。
2. 在伺服器專案中，開啟 **Controllers** > **TodoItemController.cs**，並新增下列 using 陳述式：
   
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
   
        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);
   
        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";
   
        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);
   
            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }
   
    插入新的項目時，這會傳送包含 item.Text 的範本通知。
4. 發佈伺服器專案。

### <a name="nodejs"></a>Node.js 後端專案
1. 如果您還沒這麼做，請下載[快速入門後端專案](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)或使用 [Azure 入口網站中的線上編輯器](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。
2. 在 todoitem.js 中，以下列程式碼取代現有的程式碼：
   
        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
   
        var table = azureMobileApps.table();
   
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
   
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
   
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
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
   
    插入新的項目時，這會傳送包含 item.text 的範本通知。
3. 當您在本機電腦上編輯檔案時，請重新發佈伺服器專案。

<!---HONumber=AcomDC_0629_2016-->