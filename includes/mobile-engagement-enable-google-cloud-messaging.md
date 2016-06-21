
###利用 API 金鑰建立 Google 雲端通訊專案

>[AZURE.NOTE] 若要完成此程序，您必須要有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

1. 瀏覽至 [Google 雲端主控台](https://console.developers.google.com/project)，然後使用您的 Google 帳戶認證登入。

2. 按一下 [前往專案]，然後按一下 [建立專案]。
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. 輸入專案名稱。

4. 記下顯示在 [專案名稱] 文字方塊下的專案號碼。稍後在本教學課程中，您會需要將此號碼填入 Android 資訊清單檔案中。![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. 按一下 [建立]。

6. 在左欄中，確定已選取 [總覽]，然後按一下 [行動服務類 API] 底下的 [Google 雲端通訊]。接著，在下一頁上按一下 [啟用]。

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. 在下一頁上按一下 [前往「憑證」]，並在接下來的頁面上，從第一個下拉式方塊中選取 [Google 雲端通訊]，然後從第二個下拉式方塊中選取 [網路伺服器]，接著按一下 [我需要哪些憑證？]

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. 在 [將憑證新增至您的專案] 頁面上，按一下 [建立 API 金鑰]。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. 記下 [API 金鑰] 的值。您稍後會使用此 API 金鑰值來設定 [原生推送] 區段。現在按一下 [完成]。

<!---HONumber=AcomDC_0608_2016-->