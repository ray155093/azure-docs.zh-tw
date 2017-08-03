
### <a name="create-an-application-express"></a>建立應用程式 (快速)
現在您需要在 Microsoft 應用程式註冊入口網站註冊應用程式：

1.  透過 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure) 註冊您的應用程式
2.  輸入應用程式的名稱和您的電子郵件
3.  確定已選取 [引導式設定] 選項
4.  依照指示取得應用程式識別碼並貼到您的程式碼中

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>將您的應用程式註冊資訊新增到方案 (進階)

1. 前往 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app) 註冊應用程式
2. 輸入應用程式的名稱和您的電子郵件 
3. 確定已取消選取 [引導式設定] 選項
4.  按一下 `Add Platform`，然後選取 `Web`
5. 將重新導向 URL 新增到您的應用程式。 重新導向 URL 是以您的 Web 伺服器為基礎的 `index.html` 頁面 URL
6. 按一下 [儲存] 

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>使用 SSL 取得重新導向 URL 的 Visual Studio 指示
> 如果您使用 Visual Studio，請依照下列指示，將專案設定為使用 SSL，然後使用 SSL URL 來設定應用程式的註冊資訊：
> 1.    在「方案總管」中，選取專案並查看 `Properties` 視窗 (如果您沒有看到 [屬性] 視窗，請按 F4)
> 2.    將 `SSL Enabled` 變更為 `True`
> 3.    將此值從 `SSL URL` 複製到剪貼簿：<br/> ![專案屬性](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    選取 `Project` 功能表，然後選取 `{Project} Properties...` (其中 {Project} 是您的專案名稱)
> 5.    開啟 `Web` 索引標籤
> 6.    在 `Project Url` 欄位中貼上 `SSL URL` 的值
> 7.    切換回應用程式註冊入口網站，並貼上 `Redirect URL` 中的值作為重新導向 URL，然後按一下 [儲存]


#### <a name="configure-your-javascript-spa-application"></a>設定您的 JavaScript SPA 應用程式

1.  建立名為 `msalconfig.js` 的檔案，其中包含應用程式註冊資訊。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)，以滑鼠右鍵按一下並選取：`Add` > `New Item` > `JavaScript File`。 將它命名為 `msalconfig.js`
2.  將下列程式碼新增至 `msalconfig.js` 檔案：

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
用您剛剛註冊的應用程式識別碼取代 <code>Enter_the_Application_Id_here</code>
</li>
</ol>