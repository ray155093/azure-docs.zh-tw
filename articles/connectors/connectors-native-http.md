<properties
	pageTitle="在邏輯應用程式中新增 HTTP 動作 | Microsoft Azure"
	description="HTTP 動作與屬性的概觀"
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# 開始使用 HTTP 動作

您可以利用 HTTP 動作來延伸組織的工作流程，並透過 HTTP 與任何端點通訊。

您可以：

- 建立會在您管理的網站故障時啟動 (觸發程序) 的邏輯應用程式工作流程。
- 透過 HTTP 與任何端點通訊，將工作流程延伸至其他服務。

若要使用邏輯應用程式中的 HTTP 動作來開始作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 使用 HTTP 觸發程序

觸發程序是一個事件，可用來啟動邏輯應用程式中定義的工作流程。[深入了解觸發程序](connectors-overview.md)。

以下是如何在邏輯應用程式設計工具中設定 HTTP 觸發程序的範例順序。

1. 在邏輯應用程式中新增 HTTP 觸發程序。
2. 為想要輪詢的 HTTP 端點填入參數。
3. 修改其輪詢頻率的循環間隔。
4. 邏輯應用程式現在會在每個檢查期間傳回任何內容時引發。

![HTTP 觸發程序](./media/connectors-native-http/using-trigger.png)

### HTTP 觸發程序的運作方式

HTTP 觸發程序會以循環間隔呼叫 HTTP 端點。根據預設，任何小於 300 的 HTTP 回應碼均會導致執行邏輯應用程式。您可以在程式碼檢視中新增條件，讓它在 HTTP 呼叫之後進行評估，以判斷是否應該引發邏輯應用程式。以下是 HTTP 觸發程序的範例，它會在每次傳回的狀態碼大於或等於 `400` 時觸發。

```javascript
"Http":
{
	"conditions": [
		{
			"expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
		}
	],
	"inputs": {
		"method": "GET",
		"uri": "https://blogs.msdn.microsoft.com/logicapps/",
		"headers": {
			"accept-language": "en"
		}
	},
	"recurrence": {
		"frequency": "Second",
		"interval": 15
	},
	"type": "Http"
}
```

如需關於 HTTP 觸發程序參數的詳細資料，可在 [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger) 取得。

## 使用 HTTP 動作

動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作](connectors-overview.md)。

1. 選取 [新增步驟] 按鈕。
2. 選擇 [新增動作]。
3. 在動作搜尋方塊中，輸入 **http** 以列出 HTTP 動作。

	![選取 HTTP 動作](./media/connectors-native-http/using-action-1.png)

4. 新增 HTTP 呼叫所需的任何參數。

	![完成 HTTP 動作](./media/connectors-native-http/using-action-2.png)

5. 按一下工具列左上角以儲存。您的邏輯應用程式會儲存並發佈 (啟動)。

## HTTP 觸發程序

以下是此連接器所支援觸發程序的詳細資料。HTTP 連接器有一個觸發程序。

|觸發程序|說明|
|---|---|
|HTTP|進行 HTTP 呼叫並傳回回應內容。|

## HTTP 動作

以下是此連接器所支援動作的詳細資料。HTTP 連接器有一個可能的動作。

|動作|說明|
|---|---|
|HTTP|進行 HTTP 呼叫並傳回回應內容。|

## HTTP 詳細資料

下表說明動作的必要與選擇性輸入欄位，以及與使用動作相關聯的對應輸出詳細資料。


#### HTTP 要求
以下是動作的輸入欄位，可進行 HTTP 輸出要求。標示 * 代表必要欄位。

|顯示名稱|屬性名稱|說明|
|---|---|---|
|方法 *|method|要使用的 HTTP 指令動詞|
|URI*|uri|HTTP 要求的 URI|
|標頭|headers|要包含的 HTTP 標頭的 JSON 物件|
|內文|body|HTTP 要求本文|
|驗證|authentication|詳細資料在[驗證](#authentication)一節中|
<br>

#### 輸出詳細資料

以下是 HTTP 回應的輸出詳細資料。

|屬性名稱|資料類型|說明|
|---|---|---|
|Headers|物件|回應標頭|
|Body|物件|回應物件|
|Status Code|整數|HTTP 狀態碼|

## 驗證

Azure App Service 的 Logic Apps 功能可讓您針對 HTTP 端點使用不同類型的驗證。您可以將此驗證搭配 **HTTP**、**[HTTP + Swagger](./connectors-native-http-swagger.md)** 和 **[HTTP Webhook](./connectors-native-webhook.md)** 連接器使用。下列是可設定的驗證類型︰

* [基本驗證](#basic-authentication)
* [用戶端憑證驗證](#client-certificate-authentication)
* [Azure Active Directory (Azure AD) OAuth 驗證](#azure-active-directory-oauth-authentication)

#### 基本驗證

以下是基本驗證所需的驗證物件。標示 * 代表必要欄位。

|屬性名稱|資料類型|說明|
|---|---|---|
|類型*|type|驗證類型 (若為基本驗證必須是 `Basic`)|
|使用者名稱*|username|要驗證的使用者名稱|
|密碼*|password|要驗證的密碼|

>[AZURE.TIP] 如果您要使用無法從定義中擷取的密碼，請使用 `securestring` 參數和 `@parameters()` [工作流程定義函數](http://aka.ms/logicappdocs)。

因此，您會在驗證欄位建立一個類似以下的物件︰

```javascript
{
	"type": "Basic",
	"username": "user",
	"password": "test"
}
```

#### 用戶端憑證驗證

以下是用戶端憑證驗證需要的驗證物件。標示 * 代表必要欄位。

|屬性名稱|資料類型|說明|
|---|---|---|
|類型*|type|驗證類型 (若為 SSL 用戶端憑證，必須是 `ClientCertificate`)|
|PFX*|pfx|Base 64 編碼的個人資訊交換 (PFX) 檔案內容|
|密碼*|password|存取 PFX 檔案的密碼|

>[AZURE.TIP] 使用 `securestring` 參數和 `@parameters()` [工作流程定義函數](http://aka.ms/logicappdocs)，您就能在儲存邏輯應用程式後，使用無法在定義中讀取的參數。

例如：

```javascript
{
	"type": "ClientCertificate",
	"pfx": "aGVsbG8g...d29ybGQ=",
	"password": "@parameters('myPassword')"
}
```

#### Azure AD OAuth 驗證

以下是 Azure AD OAuth 驗證所需的驗證物件。標示 * 代表必要欄位。

|屬性名稱|資料類型|說明|
|---|---|---|
|類型*|type|驗證類型 (若為 Azure AD OAuth 必須是 `ActiveDirectoryOAuth`)|
|租用戶*|tenant|Azure AD 租用戶的租用戶識別碼|
|對象*|audience|設定為 `https://management.core.windows.net/`|
|用戶端識別碼*|clientId|Azure AD 應用程式的用戶端識別碼|
|密碼*|secret|要求權杖之用戶端的密碼|

>[AZURE.TIP] 使用 `securestring` 參數和 `@parameters()` [工作流程定義函數](http://aka.ms/logicappdocs)，您就能使用儲存後無法在定義中讀取的參數。

例如：

```javascript
{
	"type": "ActiveDirectoryOAuth",
	"tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
	"audience": "https://management.core.windows.net/",
	"clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
	"secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## 後續步驟

立即試用平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。您可以查看我們的 [API 清單](apis-list.md)，以探索 Logic Apps 中其他可用的連接器。

<!---HONumber=AcomDC_0810_2016------>