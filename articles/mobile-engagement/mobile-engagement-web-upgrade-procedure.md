<properties
	pageTitle="Azure Mobile Engagement Web SDK 升級程序 | Microsoft Azure"
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
	ms.date="06/07/2016"
	ms.author="piyushjo" />


# Azure Mobile Engagement Web SDK 升級程序

如果您已經整合我們的舊版 SDK 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您有錯過幾個版本的 SDK，您必須遵循幾個步驟。例如，如果您要從 1.4.0 移轉到 1.6.0，必須先遵循「從 1.4.0 到 1.5.0」的程序，然後再依照「從 1.5.0 到 1.6.0」的程序進行。

不論您升級開始的版本為何，都必須將 `azure-engagement.js` 替換為新的。

## 從 1.2.1 到 2.0.0

以下說明如何將 SDK 整合從 Capptain SAS 提供的 Capptain 服務，移轉到由 Azure Mobile Engagement 提供的應用程式內。如果您是從較早版本移轉，請參閱 Capptain 網站，先移轉到 1.2.1 後再套用以下程序。

此版本的 Engagement Web SDK 不支援 samsung-tv、OperaTV、webOS 及 Reach 功能。

>[AZURE.IMPORTANT] Capptain 和 Mobile Engagement 是不同的服務，而以下程序只適用於移轉用戶端應用程式。移轉應用程式中的 SDK「不會」將您的資料從 Capptain 伺服器移轉到 Mobile Engagement 伺服器。

### JavaScript 檔案

將 `capptain-sdk.js` 檔案以 `azure-engagement.js` 檔案取代，並相應地更新您的指令碼匯入。

### 移除 Capptain Reach

此 Engagement Web SDK 不支援 Reach 功能，如果您已在應用程式中整合 Capptain Reach，則您必須將它移除。

從您的頁面移除 Reach css 匯入，並移除相關的 css 檔案 (預設為 capptain-reach.css)。

刪除 Reach 資源：關閉影像 (預設為 capptain-close.png) 和品牌圖示 (預設為 capptain-notification-icon)。

移除 App 內通知的 Reach UI，預設配置看起來像是：

	<!-- capptain notification -->
	<div id="capptain_notification_area" class="capptain_category_default">
	  <div class="icon">
	    <img src="capptain-notification-icon.png" alt="icon" />
	  </div>
	  <div class="content">
	    <div class="title" id="capptain_notification_title"></div>
	    <div class="message" id="capptain_notification_message"></div>
	  </div>
	  <div id="capptain_notification_image"></div>
	  <div>
	    <button id="capptain_notification_close">Close</button>
	  </div>
	</div>

移除文字\\Web 通知和投票的 Reach UI，預設配置看起來像是：

	<div id="capptain_overlay" class="capptain_category_default">
	  <button id="capptain_overlay_close">x</button>
	  <div id="capptain_overlay_title"></div>
	  <div id="capptain_overlay_body"></div>
	  <div id="capptain_overlay_poll"></div>
	  <div id="capptain_overlay_buttons">
	    <button id="capptain_overlay_exit"></button>
	    <button id="capptain_overlay_action"></button>
	  </div>
	</div>

若有的話，請從您的設定中移除 `reach` 物件。它看起來如下：

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

移除所有其他 Reach 自訂，例如類別。

### 移除已被取代的 API

在 SDK 的 Engagement 版本中，某些來自 Capptain 的 API 已被取代。

移除針對下列 API 的所有呼叫：`agent.connect`、`agent.disconnect`、`agent.pause`、`agent.sendMessageToDevice`。

若有的話，請從您的 Capptain 設定移除下列回呼：`onConnected`、`onDisconnected`、`onDeviceMessageReceived`、`onPushMessageReceived`。

### 組態

現在 Engagement 使用連接字串來設定 SDK 識別碼，例如應用程式識別碼。

將應用程式識別碼以您的連接字串取代，同時請注意到 SDK 設定的全域物件將從 `capptain` 移至 `azureEngagement`。

移轉前：

	window.capptain = {
	  appId: ...,
	  [...]
	};

移轉後：

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  [...]
	};

您應用程式的連接字串會顯示在 Azure 入口網站。

### JavaScript API

全域 JavaScript 物件 `window.capptain` 已被重新命名為 `window.azureEngagement`，但您可以針對 API 呼叫使用 `window.engagement` 別名 (您無法使用該別名來定義 SDK 設定)。

例如：`capptain.deviceId` 會變成 `engagement.deviceId`，`capptain.agent.startActivity` 會變成 `engagement.agent.startActivity` 等等...

<!---HONumber=AcomDC_0615_2016-->