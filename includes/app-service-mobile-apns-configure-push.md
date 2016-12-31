

1. 在您的 Mac 上啟動 **Keychain Access**。 在左側導覽列的 [類別] 下，開啟 [我的憑證]。 尋找您在上一節中下載的 SSL 憑證，並公開其內容。 僅選取憑證 (不選取私密金鑰)，然後[將它匯出](https://support.apple.com/kb/PH20122?locale=en_US)。
2. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [瀏覽全部] > [應用程式服務]，再按一下 [Mobile Apps 後端]。 在 [設定] 中，按一下 [App Service 推播]，然後按一下通知中樞名稱。 移至 [Apple 推播通知服務] > [上傳憑證]。 上傳 .p12 檔案，選取正確的**模式** (根據您稍早的用戶端 SSL 憑證為生產或沙箱)。 儲存任何變更。

您的服務現在已設定成在 iOS 上使用推播通知。

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png


<!--HONumber=Dec16_HO2-->


