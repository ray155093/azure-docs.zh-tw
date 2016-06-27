<properties
	pageTitle="Azure Mobile Engagement Web SDK API | Microsoft Azure"
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

# 如何在 Web 應用程式中使用 Engagement API

本文件是[如何在 Web 應用程式上整合 Engagement](mobile-engagement-web-integrate-engagement.md) 文件的補充。它會提供關於如何使用 Engagement API 來回報您應用程式的統計資料之詳細資訊。

Engagement API 是由 `engagement.agent` 物件所提供。`engagement` 為預設的 Engagement SDK 別名，並能從 SDK 設定重新定義。

## Engagement 概念

以下部分簡要說明適用於 Web 平台的 [Mobile Engagement 概念](mobile-engagement-concepts.md)。

### `Session`和`Activity`

如果使用者在兩個活動之間維持閒置超過幾秒鐘，其活動序列會分割成兩個相異的工作階段。這幾秒被稱為「工作階段逾時」。

如果您的 Web 應用程式不自行宣告使用者活動的結束 (透過呼叫 `engagement.agent.endActivity` 函數)，Engagement 伺服器將會在應用程式頁面關閉後的 3 分鐘之內自動讓使用者工作階段到期。這個行為被稱為伺服器「工作階段逾時」。

### `Crash`

沒有自動化的 JavaScript 未攔截例外狀況報告。不過，您可以透過使用 `sendCrash` 函數來手動報告當機 (如下所示)。

## 報告活動

### 使用者啟動新的活動

	engagement.agent.startActivity("MyUserActivity");

每當使用者活動變更，您就需要呼叫 `startActivity()`。第一次呼叫此函數會啟動新的使用者工作階段。

### 使用者結束其目前的活動

	engagement.agent.endActivity();

使用者完成最後一個活動時，您至少需要呼叫 `endActivity()` 一次。這會通知 Engagement SDK，說明使用者目前處於閒置狀態，且工作階段逾時到期時就得關閉使用者工作階段 (如果您在工作階段逾時到期前就呼叫 `startActivity()`，工作階段只會繼續)。

在 Web 環境內攔截使用者活動的結束通常很困難或無法達成 (沒有導覽視窗關閉時的可靠呼叫)。這就是為什麼 Engagement 伺服器會在應用程式頁面關閉後，於 3 分鐘之內自動讓使用者工作階段到期。

## 報告事件

### 工作階段事件

工作階段事件通常用來報告在其工作階段期間由使用者所執行的動作。

**不含額外資料的範例：**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login');
	  // [...]
	}

**含額外資料的範例：**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login', {user: 'alice'});
	  // [...]
	}

### 獨立事件

與工作階段事件相反，獨立的事件可能發生在工作階段的內容之外。

針對那種情況，請使用 ``engagement.agent.sendEvent``，而不是 ``engagement.agent.sendSessionEvent``。

## 報告錯誤

### 工作階段錯誤

工作階段錯誤通常用來報告在其工作階段期間影響使用者的錯誤。

**不含額外資料的範例：**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short');
	  }
	  // [...]
	}

**含額外資料的範例：**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short', {length: 4});
	  }
	  // [...]
	}

### 獨立錯誤

不同於工作階段錯誤，獨立錯誤可以出現在工作階段的內容之外。

針對那種情況，請使用 `engagement.agent.sendError`，而不是 `engagement.agent.sendSessionError`。

## 報告工作

### 範例

假設您想要監視 Ajax 要求：
			
	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	  // [...]
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### 報告於工作期間發生的錯誤

錯誤可能與正在執行的工作關聯，而不是與目前的使用者工作階段關聯。

**範例：**

假設您想要在 Ajax 要求失敗時報告錯誤：

	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	    // [...]
	    if (xhr.status == 0 || xhr.status >= 400) {
	      engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
	    }
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### 在工作期間報告事件

透過 `engagement.agent.sendJobEvent` 函數，事件可以與執行中的工作相關，而不是與目前的使用者工作階段相關。

此函數的運作方式與 `engagement.agent.sendJobError` 完全相同。

### 報告當機

`sendCrash` 函數是用來手動報告當機。

`crashid` 引數是用來識別當機類型的字串。`crash` 引數通常是以字串顯示的當機堆疊追蹤。

	engagement.agent.sendCrash(crashid, crash);

## 額外的參數

可以附加任意資料到事件、錯誤、活動或作業。

此資料可以是任何 JSON 物件 (不是陣列或基本類型)。

**範例**

	var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
	engagement.agent.sendEvent("video_clicked", extras);

### 限制

#### 之間的信任

物件中的每個索引鍵都必須符合下列規則運算式：

	^[a-zA-Z][a-zA-Z_0-9]*

這表示索引鍵必須至少以一個字母開頭，後面連接字母、數字或底線 (\_)。

#### 值

值被限制為字串、數字及布林型別。

#### 大小

額外項目限制為一次呼叫 **1024** 個字元 (由 SDK 以 JSON 編碼之後)。

## 報告應用程式資訊

您可以使用 `sendAppInfo()` 函式手動報告追蹤資訊 (或是任何其他應用程式特定資訊)。

請注意，這些資訊可以累加地傳送：只有指定的索引鍵的最新值會保留給指定的裝置。

就像事件的額外項目，任何 JSON 物件皆可以用來摘要應用程式資訊，請注意陣列或子物件會被視為一般字串 (使用 JSON 序列化)。

### 範例

以下是傳送使用者性別和出生日期的程式碼範例：

	var appInfos = {"birthdate":"1983-12-07","gender":"female"};
	engagement.agent.sendAppInfo(appInfos);

### 限制

#### 之間的信任

物件中的每個索引鍵都必須符合下列規則運算式：

	^[a-zA-Z][a-zA-Z_0-9]*

這表示索引鍵必須至少以一個字母開頭，後面連接字母、數字或底線 (\_)。

#### 大小

應用程式資訊限制為一次呼叫 **1024** 個字元 (由 SDK 以 JSON 編碼之後)。

在上述範例中，傳送到伺服器的 JSON 會是 44 個字元：

	{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=AcomDC_0615_2016-->