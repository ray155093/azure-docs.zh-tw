<properties
	pageTitle="Azure Mobile Engagement Web SDK 整合 | Microsoft Azure"
	description="Azure Mobile Engagement Web SDK 的最新更新與程序"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#如何在 Web 應用程式中整合 Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

此程序描述在您的 Web 應用程式中，啟動 Engagement 的分析和監視功能時，最簡單的方法。

下列步驟足以啟動計算使用者、工作階段、活動、當機和技術相關之所有統計資料所需的記錄檔報表。用來計算事件、錯誤及工作等其他統計資料所需的記錄檔報告必須使用 Engagement API 手動完成 (請參閱[如何在 Web 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-web-use-engagement-api.md))，因為這些是與應用程式相依的統計資料。

## 簡介

請從[這裡](http://aka.ms/P7b453)下載 Web SDK。該 SDK 是以名為 **azure-engagement.js** 的單一 JavaScript 檔案隨附，您必須將它包含在網站或 Web 應用程式的每個頁面中。

此指令碼**必須**在您需要撰寫以為應用程式設定 Engagement 的指令碼或程式碼片段**之後**載入。

## 瀏覽器相容性

Engagement Web SDK 使用原生 JSON 編碼/解碼及跨網域 AJAX 要求 (仰賴 W3C CORS 規格)。

* Edge 12+
* IE 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## 設定 Engagement

撰寫能建立全域 **azureEngagement** JavaScript 物件的指令碼，如下所示。
 
由於您的網站可能包含多個頁面，此範例將假設此指令碼也已包含在每個頁面中，我們在此程序中將它命名為 `azure-engagement-conf.js`。

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	};

您應用程式的 `connectionString` 會顯示在 Azure 入口網站。

> [AZURE.NOTE] `appVersionName` 和 `appVersionCode` 為選擇性，我們建議您設定它們，好讓分析能夠處理版本資訊。

## 在頁面中包含 Engagement 指令碼

	<head>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</head>

或

	<body>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</body>

## Alias

一旦載入，SDK 指令碼會建立 **engagement** 別名以存取 SDK API (它不能被用來定義 SDK 設定)。此別名將會做為此文件中的參考使用。

請注意，如果預設別名與另一個來自您頁面的全域變數發生衝突，您可以在載入 SDK 之前於設定中將它重新定義，如下所示：

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	  alias:'anotherAlias'
	};

## 基本報告

### 工作階段追蹤

Engagement 工作階段被分成一系列由某個名稱所識別的活動。

在傳統網站上，我們建議您在網站的每個頁面上宣告不同的活動。在永遠不會變更目前頁面的網站或 Web 應用程式中，您可能會想要以更加細緻的方式追蹤活動。

無論如何，若要開始或變更目前的使用者活動，請呼叫 `engagement.agent.startActivity` 函數，如此範例所示：

	<body onload="yourOnload()">

	<!-- -->

	yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
	};

已開啟的工作階段將會在應用程式頁面關閉後，於 3 分鐘之內由 Engagement 伺服器自動結束。

或者，您也可以透過呼叫 `engagement.agent.endActivity` 來手動結束工作階段，這將會把目前的使用者活動設定為「閒置」，並會在 10 秒後結束該工作階段，除非期間有對 `engagement.agent.startActivity` 做出新的呼叫並使工作階段繼續。
 
這個 10 秒的延遲可以在全域 **engagement** 物件中設定：

	engagement.sessionTimeout = 2000; // 2 seconds
	// or
	engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] `engagement.agent.endActivity` 無法在 `onunload` 回呼中使用，因為此階段並無法做出 Ajax 呼叫。

## 進階報告

此外，如果您想要報告應用程式特定的 `events`、`errors` 及 `jobs`，您必須透過 `engagement.agent` 物件使用 Engagement API。

Engagement API 允許使用所有 Engagement 的進階功能，詳情請見[如何在 Web 應用程式中使用進階的 Mobile Engagement 標記 API](mobile-engagement-web-use-engagement-api.md)。

## 自訂用於 AJAX 呼叫的 URL

您可以自訂 SDK 使用的 URL。如需重新定義記錄檔 URL (用於記錄的 SDK 端點) 的範例，您可以透過下列方式複寫設定：

	window.azureEngagement = {
	  ...
	  urls: {
	    ...        
	    getLoggerUrl: function() {
	    return 'someProxy/log';
	    }
	  }
	};

如果您的 URL 函數傳回開頭為 `/`、`//`、`http://` 或 `https://` 的字串，便代表沒有使用預設配置。

根據預設，那些 URL 會使用 `https://` 配置。如果您想要自訂預設配置，請透過下列方式複寫設定：

	window.azureEngagement = {
	  ...
	  urls: {
        ...	     
	    scheme: '//'
	  }
	};

<!---HONumber=AcomDC_0615_2016-->