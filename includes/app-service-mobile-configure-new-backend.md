
1. 按一下 [應用程式服務] > 選取您的行動裝置應用程式後端 > 按一下 [快速入門] > 您的用戶端平台 (iOS、Android、Xamarin、Cordova)。

![已醒目提示 [Mobile Apps 快速入門] 的 Azure 入口網站][quickstart]

2. 如果未設定資料庫的連線，您必須建立一個資料連線。

![內含 Mobile Apps 連線到 BD 的 Azure 入口網站][connect]

  * 建立新的 SQL Database 和伺服器。

  ![內含 Mobile Apps 建立新 BD 和伺服器的 Azure 入口網站][server]

  * 等到成功建立資料連線為止。

  ![內含有關資料連線建立之 Mobile Apps 通知的Azure 入口網站][notification]

  * 資料連線必須成功。

  ![內含有關資料連線建立之 Mobile Apps 通知的Azure 入口網站][already-connection]

3. 在 **2.建立資料表 API** 之下，選取 Node.js 作為**後端語言**。 接受通知，然後按一下 [建立 TodoItem 資料表] 。 這會在您的資料庫中建立新的 *TodoItem* 資料表。 請記住，將現有後端切換到 Node.js 將會覆寫所有內容！ 若是要建立 .NET 後端，請[遵循此處指示][instructions]。

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
