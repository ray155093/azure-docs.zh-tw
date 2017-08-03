
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft Authentication Library (MSAL) 取得 Microsoft 圖形 API 的權杖

將下列程式碼新增至 `<body>` 內的 `index.html`：

```html
<script type="text/javascript">

    var graphAPIMeEndpoint = "https://graph.microsoft.com/v1.0/me";
    var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

    // Initialize application
    var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID);

    // Set redirect URI
    userAgentApplication.redirectUri = msalconfig.redirectUri;

    displayUserInfo();

    function displayUserInfo() {
        var user = userAgentApplication.getUser();
        if (user) {
            // Display the user info
            var userInfoElement = document.getElementById("userInfo");
            userInfoElement.parentElement.classList.remove("hidden");
            userInfoElement.innerHTML = JSON.stringify(user, null, 4);

            // Show Sign-Out button
            document.getElementById("signOutButton").classList.remove("hidden");
        }
    }

    function callGraphAPI() {
        if (userAgentApplication.getAllUsers().length === 0) {
            userAgentApplication.loginPopup()
                .then(function (token) {
                    console.log(token);
                    displayUserInfo();
                    callGraphAPI();
                }, function (error) {
                    showError("login", error, document.getElementById("errorMessage"));
                });
        } else {
            var responseElement = document.getElementById("graphResponse");
            responseElement.parentElement.classList.remove("hidden");
            responseElement.innerText = "Calling Graph ...";
            callWebApiWithScope(graphAPIMeEndpoint,
                graphAPIScopes,
                responseElement,
                document.getElementById("errorMessage"),
                document.getElementById("accessToken"));
        }
    }

    function callWebApiWithScope(endpoint, scope, responseElement, errorElement, showTokenElement) {
        userAgentApplication.acquireTokenSilent(scope)
            .then(function (token) {
                callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
            }, function (error) {
                if (error.indexOf("interaction_required" !== -1)) {
                    userAgentApplication.acquireTokenPopup(scope).then(function(token) {
                            callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
                        },
                        function(error) {
                            showError(endpoint, error, errorElement);
                        });
                } else {
                    showError(endpoint, error, errorElement);
                }
            });
    }

    function showAPIResponse(data, token, responseElement, showTokenElement) {
        console.log(data);
        responseElement.innerHTML = JSON.stringify(data, null, 4);
        if (showTokenElement) {
            showTokenElement.parentElement.classList.remove("hidden");
            showTokenElement.innerHTML = token;
        }
    }

    function showError(endpoint, error, errorElement) {
        console.error(error);
        var formattedError = JSON.stringify(error, null, 4);
        if (formattedError.length < 3) {
            formattedError = error;
        }
        errorElement.innerHTML = "Error calling " + endpoint + ": " + formattedError;
    }
</script>
```

<!--start-collapse-->
### <a name="more-information"></a>相關資訊
#### <a name="sign-in-the-user"></a>登入使用者
在使用者第一次按一下 [呼叫 Microsoft Graph API] 按鈕之後，系統會執行 `loginPopup` 讓使用者登入。 此方法會顯示一個瀏覽器視窗，提示使用者登入。 由於成功登入，所以 MSAL 會快取使用者資訊並傳回權杖。 此權杖也稱為「ID 權杖」且包含使用者的基本資訊，例如使用者顯示名稱。 如果您打算將此權杖所提供的任何資料用於任何目的，您必須確定後端伺服器已驗證此權杖，以保證權杖是發給您應用程式的有效使用者。

本指南所產生的 SPA 不會使用直接 ID 權杖 - 反而會呼叫 `acquireTokenSilent` 和/或 `acquireTokenPopup`，以取得用來查詢 Microsoft Graph API 的「存取權杖」。 如果您需要可驗證 ID 權杖的範例，請看一下 GitHub 中的 [這個](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2範例") 範例應用程式 – 此範例使用 ASP.NET Web API 進行權杖驗證。

#### <a name="getting-a-user-token-interactively"></a>以互動方式取得使用者權杖
呼叫 `acquireTokenPopup(scope)` 方法時會顯示一個瀏覽器視窗，提示使用者登入。 當使用者第一次需要存取受保護的資源，或取得權杖的無訊息作業失敗 (例如使用者的密碼過期) 時，應用程式通常會要求使用者以互動方式登入。

#### <a name="getting-a-user-token-silently"></a>以無訊息方式取得使用者權杖
` acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 `loginPopup` 在第一次執行之後，`acquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源，並且會以無訊息方式進行要求或更新權杖的呼叫。
最後，`acquireTokenSilent` 將會失敗，例如，使用者已在其他裝置上變更其密碼。 您的應用程式可以透過兩種方式處理此例外狀況：

1.  立即針對 `acquireTokenPopup` 進行呼叫，這會促使系統提示使用者登入。 此模式通常用於應用程式中沒有離線內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例應用程式會使用此模式。
2. 應用程式也可以提供視覺指示，讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `acquireTokenSilent`。 此方式通常用於使用者可以使用應用程式的其他功能，不需要因此中斷作業的情況，例如，應用程式中有離線內容可供使用者使用。 在此案例中，使用者可以決定他們要登入以存取受保護資源，或重新整理過時資訊的時機，或者您的應用程式可以決定在網路暫時中斷之後恢復連線時重試 `acquireTokenSilent`。

`acquireTokenSilent` 失敗的另一種情況，就是使用者尚未同意呼叫所要求的範圍時。 例如，使用者第一次執行本指南所產生的應用程式時，系統會提供 user.read 範圍作為參數。 因為登入的使用者未曾同意讀取其設定檔，所以 `acquireTokenSilent` 會產生含有 `interaction_required` 的例外狀況 - 這通常表示應該呼叫 `acquireTokenPopup` 來解決此問題。 在此情況下，將會建立快顯視窗以顯示所需範圍的同意畫面。

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft 圖形 API

將下列程式碼新增至 `<body>` 內的 index.html：

```html
 <script type="text/javascript">
function callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    // Note that fetch API is not available in all browsers
    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showAPIResponse(data, token, responseElement, showTokenElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showError(endpoint, data, errorElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error, errorElement);
        });
}
</script>
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的詳細資訊

在本指南建立的範例應用程式中，`callWebApiWithToken()` 方法是用來針對受保護資源提出 HTTP `GET` 要求，那些受保護資源需要權杖才能存取，然後再將內容傳回給使用者。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 對於本指南建立的範例應用程式，資源為 Microsoft 圖形 API *me* 端點，它會顯示使用者的設定檔資訊。

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>新增方法來將使用者登出

將下列程式碼新增至 `<body>` 內的 index.html：

```html
 <script type="text/javascript">
    function signOut() {
        userAgentApplication.logout();
    }
</script>
```
