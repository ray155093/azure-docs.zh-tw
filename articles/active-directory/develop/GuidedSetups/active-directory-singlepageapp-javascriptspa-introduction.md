
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>從 JavaScript 單一頁面應用程式 (SPA) 呼叫 Microsoft Graph API

本指南示範 JavaScript 單一頁面應用程式 (SPA)如何登入個人和工作帳戶，取得存取權杖，以及呼叫 Microsoft Graph API 或需要來自 Azure Active Directory v2 端點之存取權杖的其他 API。

### <a name="how-this-guide-works"></a>本指南使用方式

![本指南產生之範例應用程式的運作方式](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>相關資訊

本指南建立的範例應用程式可讓 JavaScript SPA 查詢 Microsoft Graph API，或查詢可接受來自 Azure Active Directory v2 端點之權杖的 Web API。 針對這個案例，在使用者登入，系統會透過授權標頭將一個權杖新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。
<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|說明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library for JavaScript 預覽|
<!--end-collapse-->
