
## <a name="add-the-applications-registration-information-to-your-app"></a>將應用程式的註冊資訊新增到您的應用程式

在此步驟中，您需要設定應用程式註冊資訊的重新導向 URL，然後將應用程式識別碼新增至 JavaScript SPA 應用程式。

### <a name="configure-redirect-url"></a>設定重新導向 URL

使用以您的 Web 伺服器為基礎的 index.html 網頁 URL 設定上述 `Redirect URL` 欄位，然後按一下 [更新]。

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>使用 SSL 取得重新導向 URL 的 Visual Studio 指示
> 如果您使用 Visual Studio，請依照下列指示，將專案設定為使用 SSL，然後使用 SSL URL 來設定應用程式的註冊資訊：
> 1.    在「方案總管」中，選取專案並查看 `Properties` 視窗 (如果您沒有看到 [屬性] 視窗，請按 F4)
> 2.    將 `SSL Enabled` 變更為 `True`
> 3.    將此值從 `SSL URL` 複製到剪貼簿：<br/> ![專案屬性](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    選取 `Project` 功能表，然後選取 `{Project} Properties...` (其中 {Project} 是您的專案名稱)
> 5.    開啟 `Web` 索引標籤
> 6.    在 `Project Url` 欄位中貼上 `SSL URL` 的值
> 7.    並且將此值貼在此頁面頂端的 `Redirect URL` 欄位中，然後按一下 [更新]


### <a name="configure-your-javascript-spa-application"></a>設定您的 JavaScript SPA 應用程式

1.  建立名為 `msalconfig.js` 的檔案，其中包含應用程式註冊資訊。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)，以滑鼠右鍵按一下並選取：`Add` > `New Item` > `JavaScript File`。 將它命名為 `msalconfig.js`
2.  將下列程式碼新增至 `msalconfig.js` 檔案：

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
