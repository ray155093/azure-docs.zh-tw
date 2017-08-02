
## <a name="setting-up-your-web-server-or-project"></a>設定您的 Web 伺服器或專案

> 想要改為下載此範例的 Visual Studio 專案嗎？ [下載專案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/master.zip)並跳至[設定步驟](#create-an-application-express)，以在執行之前先設定程式碼範例。

## <a name="prerequisites"></a>必要條件
需要有本機 Web 伺服器 (例如 [http-server](https://www.npmjs.com/package/http-server/)、[Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core)，或與 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 的 IIS Express 整合)，才能執行此引導式設定。 

本指南中的指示是以 Visual Studio 2017 為基礎，但可以自由使用任何其他開發環境或 HTML/JavaScript 編輯器。


## <a name="create-your-project-visual-studio-only"></a>建立您的專案 (僅限 Visual Studio)

如果您使用 Visual Studio 並建立新的專案，請遵循下列步驟來建立新的 Visual Studio 解決方案：
1.  在 Visual Studio 中：`File` > `New` > `Project`
2.  在 `Visual C#\Web` 之下選取 `ASP.NET Web Application (.NET Framework)`
3.  為您的應用程式命名並按一下 [確定]
4.  在 `New ASP.NET Web Application` 之下選取 `Empty`


## <a name="create-your-single-page-applications-ui"></a>建立單一頁面應用程式的 UI
1.  為您的 JavaScript SPA 建立 index.html 檔案。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)，以滑鼠右鍵按一下並選取：`Add` > `New Item` > `HTML page`，而且將它命名為 index.html
2.  將下列程式碼新增至您的頁面：
```html
<!DOCTYPE html>
<html>
    <head>
        <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
        <title>JavaScript SPA Guided Setup</title>
    </head>
    <body style="margin: 40px">
        <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphAPI()">Call Microsoft Graph API</button>
        <div id="errorMessage" class="text-danger"></div>
        <div class="hidden">
            <h3>Graph API Call Response</h3>
            <pre class="well" id="graphResponse"></pre>
        </div>
        <div class="hidden">
            <h3>Access Token</h3>
            <pre class="well" id="accessToken"></pre>
        </div>
        <div class="hidden">
            <h3>ID Token Claims</h3>
            <pre class="well" id="userInfo"></pre>
        </div>
        <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

        <script src="//secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>
        <script type="text/javascript" src="msalconfig.js"></script>
    
        <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
        <script src="//cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="//cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>
    </body>
</html>
````
