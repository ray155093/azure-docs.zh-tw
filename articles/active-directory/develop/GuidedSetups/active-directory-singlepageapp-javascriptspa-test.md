
## <a name="test-your-code"></a>測試您的程式碼

如果您使用 Visual Studio，請按 `F5` 來執行專案。 瀏覽器將會開啟並將您導引至 http://localhost:{port}，您會在其中看見 [呼叫 Microsoft Graph API] 按鈕。

如果您不是使用 Visual Studio，請確定您的 Web 伺服器已啟動，而且已在 Web 伺服器中設定包含 JavaScript Web 應用程式的資料夾。 開啟瀏覽器並輸入 http://localhost:{port}/path -其中的 port 對應至您的 Web 伺服器正在接聽的連接埠，而 path 是 index.html 的路徑。

按一下 [呼叫 Microsoft Graph API] 按鈕。 如果這是第一次，快顯視窗會顯示以提示使用者登入。
 
![範例螢幕擷取畫面](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>同意
第一次登入應用程式時，系統會向您顯示如下所示的類似同意畫面，其中包含您必須明確接受的事項：

 ![同意畫面](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)

因為您正在查詢 Microsoft Graph API，所以可能會看到另一個同意頁面。 這種情況是由「動態同意」所造成- 應用程式要求的每個範圍都需要同意。 對於本指南所產生的範例應用程式，系統會提供 user.read 範圍，因此，您必須同意此應用程式讀取使用者的設定檔。

> [!IMPORTANT]
> 目前，由於 msal javascript 的已知問題，您必須在 Chrome 和 Firefox 等瀏覽器中停用快顯封鎖程式，「動態同意」畫面才能正常運作。 使用者第一次使用 `acquireTokenPopup` 呼叫 Microsoft Graph API 時，必須停用快顯視窗封鎖程式。

### <a name="expected-results"></a>預期的結果
您應該會看到 Microsoft Graph API 回應所傳回的使用者設定檔資訊。
 
 ![結果](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

您應該也會在 [存取權杖] 和 [ID 權杖宣告] 方塊中看到取得之權杖的相關基本資訊。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 `user.read` 範圍以讀取使用者的設定檔。 根據預設值，在我們註冊入口網站上註冊的每個應用程式中都會自動新增此範圍。 Microsoft Graph 的某些其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，針對 Microsoft Graph，需要範圍 `Calendars.Read` 才能列出使用者的行事曆。 為了在應用程式內容中存取使用者的行事曆，您需要將 `Calendars.Read` 委派權限新增至應用程式註冊的資訊，然後將 `Calendars.Read` 範圍新增至 `acquireTokenSilent` 呼叫。 系統可能會在您增加範圍數目時，提示使用者同意其他事項。

如果後端 API 不需要範圍 (不建議)，您可以在 `acquireTokenSilent` 和/或 `acquireTokenPopup` 呼叫中使用 `clientId` 作為範圍。

<!--end-collapse-->
