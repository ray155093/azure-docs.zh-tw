<properties
   pageTitle="做為可呼叫端點的邏輯應用程式"
   description="如何建立並設定端點，並在 Azure App Service 的邏輯應用程式中加以使用"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/10/2016"
   ms.author="jehollan"/>


# 做為可呼叫端點的邏輯應用程式

Logic Apps 原本就能公開同步的 HTTP 端點做為觸發程序。您也可以使用可呼叫端點模式，透過邏輯應用程式中的「工作流程」將 Logic Apps 叫用為巢狀工作流程。

有 3 種類型的觸發程序可以接收要求︰

* 要求
* ApiConnectionWebhook
* HttpWebhook

本文的其餘部分會以 **request** 為例，但所有的原則同樣適用於其他 2 個類型的觸發程序。

## 將觸發程序加入您的定義
第一個步驟是將觸發程序加入您的邏輯應用程式定義，如此您就能收到連入要求。您可以搜尋設計工具中的「HTTP 要求」，以新增觸發程序卡片。您可以定義要求主體 JSON Schema，設計工具會產生權杖，協助您透過工作流程，從手動觸發程序剖析並傳送資料。我建議使用類似 [jsonschema.net](http://jsonschema.net) 的工具，以從範例主體承載產生 JSON 結構描述。

![要求觸發程序卡片][2]

儲存您的邏輯應用程式定義之後，會產生類似以下的回呼 URL：
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

此 URL 的查詢參數中包含用於驗證的 SAS 金鑰。

您也可以在 Azure 入口網站取得此端點 ︰

![][1]

或藉由呼叫︰

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## 呼叫邏輯應用程式觸發程序的端點

建立觸發程序的端點之後，您可以透過對完整 URL 的 `POST` 來觸發它。您可以在主體中包含額外的標頭及任何內容。

如果 content-type 是 `application/json`，則您將能夠從要求內部參考屬性。否則，它將會被視為單一的二進位單位，其可傳遞至其他 API，但無法在不轉換內容的情況下在工作流程內部參考。例如，如果您要傳遞 `application/xml` 內容，可以使用 `@xpath()` 進行 xpath 擷取，或使用 `@json()` to 將 XML 轉換成 JSON。[這裡提供](app-service-logic-content-type.md)使用內容類型的詳細資訊

此外，您可以在定義中指定 JSON 結構描述。這會讓設計工具產生權杖，接著您可以傳入步驟。例如，下例會讓設計工具有 `title` 和 `name` 權杖可用︰

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## 參考連入要求的內容

`@triggerOutputs()` 函數將輸出連入要求的內容。例如，它應該看起來如下：

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

您可以使用 `@triggerBody()` 捷徑，特別存取 `body` 屬性。

## 回應要求

對於某些啟動邏輯應用程式的要求，您可能想要使用某些內容來回應呼叫者。有一個名為 **response** 的新動作類型，可用來建構回應的狀態碼、主體及標頭。請注意，如果沒有**回應**圖形，則邏輯應用程式端點會「立即」回應**202 已接受**。

![HTTP 回應動作][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

回應會包含下列內容：

| 屬性 | 說明 |
| -------- | ----------- |
| StatusCode | 要回應連入要求的 HTTP 狀態碼。它可以是任何以 2xx、4xx 或 5xx 開頭的有效狀態碼。不允許 3xx 狀態碼。 | 
| body | 主體物件可以是字串、JSON 物件，甚至是上一個步驟中所參考的二進位內容。 | 
| headers | 您可以定義要包含於回應中的標頭，且數目不限 | 

邏輯應用程式中針對回應所需的所有步驟都必須在原始要求收到要求的 *60 秒*內完成，才能接到回應，**除非工作流程已呼叫為巢狀邏輯應用程式**。如果在 60 秒內未達成任何回應動作，則連入要求將會逾時，並收到 [408 用戶端逾時] HTTP 回應。針對巢狀Logic Apps，無論花費多少時間，父邏輯應用程式都會繼續等候回應，直到完成為止。

## 進階的端點組態

邏輯應用程式內建直接存取端點的支援，並一律使用 `POST` 方法來啟動邏輯應用程式執行。**HTTP 接聽程式** API 應用程式之前也支援變更 URL 區段和 HTTP 方法。您甚至可以藉由將其加入 API 應用程式主機 (已裝載 API 應用程式的 Web 應用程式)，來設定額外的安全性或自訂網域。

這項功能是透過 **API 管理**取得：
* [變更要求的方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [變更要求的 URL 區段](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* 在傳統 Azure 入口網站的 [設定] 索引標籤上設定 API 管理網域
* 設定檢查基本驗證的原則 (**需要的連結**)

## 從 2014-12-01-preview 開始的移轉摘要

| 2014-12-01-preview | 2016-06-01 |
|---------------------|--------------------|
| 按一下 [HTTP 接聽程式] API 應用程式 | 按一下 [手動觸發程序]\(不需要 API 應用程式) |
| HTTP 接聽程式設定 [自動傳送回應] | 可能包含**回應**動作或不在工作流程定義中 |
| 設定基本或 OAuth 驗證 | 透過 API 管理 |
| 設定 HTTP 方法 | 透過 API 管理 |
| 設定相對路徑 | 透過 API 管理 |
| 透過 `@triggerOutputs().body.Content` 參考連入主體 | 透過 `@triggerOutputs().body` 參考 |
| HTTP 接聽程式上的**傳送 HTTP 回應**動作 | 按一下 [回應 HTTP 要求]\(不需要 API 應用程式)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png

<!---HONumber=AcomDC_0810_2016---->