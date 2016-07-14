<properties
	pageTitle="如何使用適用於 Azure 行動應用程式的 JavaScript SDK"
	description="如何使用適用於 Azure 行動應用程式的 v"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="adrianha;ricksal"/>

# 如何使用適用於 Azure 行動應用程式的 JavaScript 用戶端程式庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南將教導您使用最新的[適用於 Azure Mobile Apps 的 JavaScript SDK] 執行一般案例。如果您不熟悉 Azure 行動應用程式，請先完成 [Azure 行動應用程式快速入門]建立後端，並建立資料表。在本指南中，我們會著重在使用 HTML/JavaScript Web 應用程式中的行動後端。

##<a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的後端。本指南假設資料表的結構描述與這些教學課程中的資料表相同。

安裝 Azure 行動應用程式 JavaScript SDK 可透過 `npm` 命令完成︰

```
npm install azure-mobile-apps-client --save
```

安裝之後，程式庫位於 `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`。將這個檔案複製到您的網站區域。

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

程式庫也可在 CommonJS 環境 (例如 Browserify 和 Webpack) 內部做為 ES2015 模組和 AMD 程式庫使用。例如：

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>作法：驗證使用者

Azure App Service 支援使用各種外部識別提供者 (Facebook、Google、Microsoft 帳戶以及 Twitter) 來驗證與授權應用程式使用者。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以在伺服器指令碼中，使用驗證的使用者的身分識別來實作授權規則。如需詳細資訊，請參閱[開始使用驗證]教學課程。

支援兩個驗證流程：伺服器流程和用戶端流程。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。用戶端流程依賴提供者專屬的 SDK，可以與裝置特有的功能深入整合，例如單一登入。

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>做法︰設定行動 App Service 以使用外部重新導向 URL。

數種類型的 JavaScript 應用程式使用回送功能來處理 OAuth UI 流程，例如，在本機執行您的服務時、在 Ionic 架構中使用即時重新載入，或重新導向至 App Service 進行驗證時。這可能會造成問題，因為根據預設，App Service 驗證只設定為允許從您的行動應用程式後端來存取。

請使用下列步驟來變更 App Service 設定，以允許從 localhost 進行驗證︰

1. 登入 [Azure 入口網站]，瀏覽至您的行動應用程式後端，然後按一下 [工具] > [資源總管] > [前往]，為您的行動應用程式後端 (網站) 開啟新的資源總管視窗。

2. 展開應用程式的 **config** 節點，然後按一下 [authsettings] > [編輯]，尋找 **allowedExternalRedirectUrls** 元素 (應該是 null)，並將它變更如下︰

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    使用您的服務的 URL 取代陣列中的 URL，在此範例中為本機 Node.js 範例服務的 `http://localhost:3000`。若是 Ripple 服務，您也可以使用 `http://localhost:4400` 或一些其他的 URL，根據您的應用程式的設定方式而定。
    
3. 在頁面頂端，按一下 [讀取/寫入]，然後按一下 [PUT] 以儲存您所做的更新。

    您仍需要將相同的回送 URL 加入至 CORS 白名單設定：

4. 回到 [Azure 入口網站]，在您的行動應用程式後端，按一下 [所有設定] > [CORS]，將回送 URL 加入至白名單，然後按一下 [儲存]。

後端更新之後，您就可以在應用程式中使用新的回送 URL。

<!-- URLs. -->
[Azure 行動應用程式快速入門]: app-service-mobile-cordova-get-started.md
[開始使用驗證]: app-service-mobile-cordova-get-started-users.md
[將驗證新增至您的應用程式]: app-service-mobile-cordova-get-started-users.md

[適用於 Azure Mobile Apps 的 JavaScript SDK]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/zh-TW/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0629_2016-->