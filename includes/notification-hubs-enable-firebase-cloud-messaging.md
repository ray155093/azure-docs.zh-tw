

1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。建立新的 Firebase 專案 (如果您還沒有 Firebase 專案的話)。
2. 專案建立之後，按一下 [將 Firebase 新增至 Android 應用程式]，並依照所提供的指示執行。

	![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. 在 Firebase 主控台中，按一下專案的齒輪圖示，再按一下 [專案設定]。

	![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. 按一下專案設定中的 [雲端通訊] 索引標籤，並複製 [伺服器金鑰] 和 [寄件者識別碼] 的值。稍後會使用這些值來設定通知中樞的存取原則，以及應用程式中的通知處理常式。
  

<!---HONumber=AcomDC_0706_2016-->