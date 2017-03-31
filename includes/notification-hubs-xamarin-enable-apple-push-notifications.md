
若要透過 Apple Push Notification Service (APNS) 註冊要進行推播通知的應用程式，您必須在 Apple 開發人員入口網站上為專案建立推播憑證、應用程式識別碼和佈建設定檔。

應用程式識別碼會包含可讓您的應用程式傳送及接收推播通知的組態設定。 這些設定將會包含應用程式傳送及接收推播通知時，向 APNS 進行驗證所需的推播通知憑證。

如需這些概念的詳細資訊，請參閱 [Apple Push Notification Service](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) 官方文件。

## <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>產生推播憑證的憑證簽署要求檔案
這些步驟會引導您建立憑證簽署要求的程序，會產生與 APNS 搭配使用的推播憑證。

1. 在您的 Mac 上，執行「鑰匙圈存取」工具。 它可從 Launch Pad 上的 [公用程式] 資料夾或 [其他] 資料夾開啟。

2. 選取 [金鑰鏈存取]，並展開 [憑證助理]，然後選取 [從憑證授權單位要求憑證...]。

      ![要求憑證](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. 選取您的 [使用者電子郵件地址] 和 [一般名稱]。

4. 請確定已選取 [儲存至磁碟]，然後選取 [繼續]。 請將 [CA 電子郵件地址] 欄位留空，因為它不是必要資訊。

      ![憑證資訊](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. 在 [另存新檔] 輸入憑證簽署要求 (CSR) 檔案的名稱。
5. 在 [其中] 選取位置，然後選取 [儲存]。

      ![儲存憑證簽署要求](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

      這會在選取的位置儲存 CSR 檔案。 (預設位置是您的桌面。)請記住您為這個檔案選擇的位置。

## <a name="register-your-app-for-push-notifications"></a>針對推播通知註冊應用程式
為您的 Apple 應用程式建立新的明確應用程式識別碼，然後為它設定推播通知。  

1. 在 Apple 開發人員中心瀏覽至 [iOS 佈建入口網站](http://go.microsoft.com/fwlink/p/?LinkId=272456)，然後使用您的 Apple ID 登入。

2. 選取 [識別碼]，然後選取 [應用程式識別碼]。

3. 選取 [+] 符號以註冊新的應用程式。

      ![註冊新的應用程式](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

4. 將新應用程式的下列三個欄位予以更新，然後選取 [繼續]：

   * **名稱**：在 [應用程式識別碼描述] 區段中，輸入應用程式的描述名稱。

   * **套件組合識別碼**：在 [明確的應用程式識別碼] 區段之下，以[應用程式分發指南](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8) (英文) 中所述的 `<Organization Identifier>.<Product Name>` 形式輸入 [套件組合識別碼]。 這必須符合 XCode、Xamarin 或 Cordova 專案中用於應用程式的識別碼。

   * **推播通知**：選取 [應用程式服務] 區段中的 [推播通知] 選項。

     ![註冊應用程式識別碼](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

5. 在**確認您的應用程式識別碼畫面**上，檢閱設定。 在您確認之後，請選取 [註冊]。

6. 提交新的應用程式識別碼之後，您會看見 [註冊完成] 畫面。 選取 [完成] 。

7. 在開發人員中心中，於 [App IDs] 下找出您建立的 App ID，並選取該資料列。 選取應用程式識別碼列，可顯示應用程式詳細資料。 然後選取底部的 [編輯] 按鈕。

8. 捲動到畫面底部，然後選取 [開發 SSL 憑證] 區段下方的 [建立憑證...] 按鈕。

      ![開發推播 SSL 憑證](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

 這將顯示 [Add iOS Certificate] 助理。

   > [!NOTE]
   > 本教學課程使用開發憑證。 註冊生產憑證時，將使用同一個程序。 確定在傳送通知時使用相同的憑證類型。
   >

9. 選取 [選擇檔案]，然後瀏覽至您為推播憑證儲存 CSR 的位置。 然後選取 [產生]。

      ![產生憑證](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

10. 在入口網站建立憑證之後，選取 [下載] 按鈕。

      ![憑證已備妥](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

       這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。

      ![下載資料夾](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

   > [!NOTE]
   > 依預設，下載的檔案是開發憑證，名稱為 **aps_development.cer**。
   >
   >
11. 按兩下下載的推播憑證 **aps_development.cer**。 這樣會將新的憑證安裝在金鑰鏈中，如下列螢幕擷取畫面所示：

       ![金鑰鏈存取](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

   > [!NOTE]
   > 憑證中的名稱可能會不同，不過字首會加上 **Apple Development iOS Push Services:** 前置詞。
   >
   >
12. 在 **Keychain Access** 中，選取您在 [憑證] 類別中建立的新推播憑證。 選取 [匯出]、為檔案命名、選取 [.p12] 格式，然後選取 [儲存]。

    請記住匯出的 .p12 推播憑證的檔案名稱和位置。 透過在 Azure 傳統入口網站將它上傳，即可將它用來向 APNS 進行驗證。 如果無法使用 **.p12** 格式選項，您可能需要重新啟動 Keychain Access。

## <a name="create-a-provisioning-profile-for-the-app"></a>建立應用程式的佈建設定檔
1. 返回 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>，並依序選取 [佈建設定檔] 和 [全部]，然後選取 **+** 按鈕建立設定檔。 如此會啟動**增 iOS 佈建** 設定檔工具。

      ![佈建設定檔](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. 在 [開發] 下，選取 [iOS 應用程式開發] 作為佈建設定檔類型，然後選取 [繼續]。

3. 在 [應用程式識別碼] 下拉式清單中，選取您建立的應用程式識別碼，然後選取 [繼續]。

      ![選取應用程式識別碼](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. 在 [選取憑證] 畫面中，選取用於程式碼簽署的開發憑證，然後選取 [繼續]。 這是簽署憑證，不是您所建立的推播憑證。

       ![Signing certificate](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. 選取要用來測試的 [裝置]，然後選取 [繼續]。

     ![新增佈建設定檔](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. 最後，在 [設定檔名稱] 中為設定檔挑選名稱，然後選取 [產生]。

      ![為設定檔命名](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
