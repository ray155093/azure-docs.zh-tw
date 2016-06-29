
###利用 API 金鑰建立 Google 雲端通訊專案

>[AZURE.NOTE] 若要完成此程序，您必須要有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

1. 瀏覽至 [Google 雲端主控台](https://console.developers.google.com/project)，然後使用您的 Google 帳戶認證登入。

2. 前往 [所有專案]，然後按一下 [建立專案]。

3. 輸入 [專案名稱]，然後按一下 [建立]

4. 建立專案後，然後務必記下**專案編號** (會是很長的數值)。您可以在您專案的 [設定] 中的 [IAM 與系統管理] 區段中找到此編號，您稍後會需要此編號。
 
	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. 現在我們將 為Google 雲端通訊平台建立金鑰，而我們的平台會使用該金鑰將通知傳送至 Android 裝置。移至 [API 管理員] 區段，然後按一下 [行動 API] 底下的 [Google 雲端通訊]。

	![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. 在下一頁上，按一下 [啟用] 按鈕。儀表板會提示您建立認證。所以按一下 [前往認證] 按鈕。

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. 從第一個下拉式方塊中選取 [Google 雲端通訊]，然後從第二個下拉式方塊中選取 [網路伺服器]，接著按一下 [我需要哪些憑證？]

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. 在 [將憑證新增至您的專案] 頁面上，按一下 [建立 API 金鑰]。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. 記下 [API 金鑰] 的值。您稍後會使用此 API 金鑰值來設定 [原生推送] 區段。現在按一下 [完成]。

<!---HONumber=AcomDC_0622_2016-->