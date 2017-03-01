---
title: "做為可呼叫端點的邏輯應用程式 | Microsoft Docs"
description: "如何建立並設定觸發端點，並在 Azure 邏輯應用程式中加以使用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: d7144208fc3e6eb1f8d3c43d8b4a5e2bcb225e58
ms.openlocfilehash: ac0c200abd110262badd04212c82be45cb0f8bfc
ms.lasthandoff: 02/22/2017


---
# <a name="logic-apps-as-callable-endpoints"></a>做為可呼叫端點的邏輯應用程式
Logic Apps 原本就能公開同步的 HTTP 端點做為觸發程序。  您也可以使用可呼叫端點模式，透過邏輯應用程式中的「工作流程」將 Logic Apps 叫用為巢狀工作流程。

有 3 種類型的觸發程序可以接收要求︰

* 要求
* ApiConnectionWebhook
* HttpWebhook

本文的其餘部分會以 **request** 為例，但所有的原則同樣適用於其他 2 個類型的觸發程序。

## <a name="adding-a-trigger-to-your-definition"></a>將觸發程序加入您的定義
第一個步驟是將觸發程序加入您的邏輯應用程式定義，如此您就能收到連入要求。  您可以搜尋設計工具中的「HTTP 要求」，以新增觸發程序卡片。 您可以定義要求主體 JSON Schema，設計工具會產生權杖，協助您透過工作流程，從手動觸發程序剖析並傳送資料。  我建議使用類似 [jsonschema.net](http://jsonschema.net) 的工具，以從範例主體承載產生 JSON 結構描述。

![要求觸發程序卡片][2]

儲存您的邏輯應用程式定義之後，會產生類似以下的回呼 URL：

``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

此 URL 的查詢參數中包含用於驗證的 SAS 金鑰。

您也可以在 Azure 入口網站取得此端點 ︰

![][1]

或藉由呼叫︰

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="changing-http-method-of-the-trigger"></a>變更觸發程序的 HTTP 方法
根據預設，Logic Apps 的要求觸發程序需要有 HTTP POST 要求。 但您可以在 `Show advanced options` 下方設定 HTTP 方法。

 > [!NOTE]
 > 只允許一種方法。

### <a name="relative-trigger-url"></a>相對的觸發程序 URL
您也可以自訂要求 URL 的相對路徑來接受參數。

1. 展開 **Request** 觸發程序的 `Show advanced options`。
 - 在 `Relative path` 下方，輸入 `customer/{customerId}`。

  ![相對的 URL 觸發程序](./media/logic-apps-http-endpoint/relativeurl.png)

2. 更新 **Respond** 動作，為使用者產生參數。
 - 您應該會看到 `customerId` 顯示於權杖選擇器中。
 - 更新回應主體以傳回 `Hello {customerId}`。

  ![相對的 URL 回應](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. 儲存邏輯應用程式，您應該會注意到要求 URL 更新以包含相對路徑。

4. 複製新的要求 URL，並將它貼到新的瀏覽器視窗中。 使用 `123` `{customerId}`，然後按 Enter。
 - 您應該會看到傳回的 `Your customer Id is 123`。

### <a name="security-for-the-trigger-url"></a>觸發程序 URL 的安全性
邏輯應用程式回呼 URL 是使用共用存取簽章安全地產生。  簽章是以查詢參數的形式傳遞，且必須在引發邏輯應用程式之前先驗證。  它是透過每個邏輯應用程式、觸發程序名稱，以及要執行之作業的秘密金鑰的唯一組合產生。  除非某人具有邏輯應用程式秘密金鑰的存取權，否則他們無法能產生有效的簽章。

## <a name="calling-the-logic-app-triggers-endpoint"></a>呼叫邏輯應用程式觸發程序的端點
建立觸發程序的端點之後，您可以透過對完整 URL 的 `POST` 來觸發它。 您可以在主體中包含額外的標頭及任何內容。

如果 content-type 是 `application/json` ，則您將能夠從要求內部參考屬性。 否則，它將會被視為單一的二進位單位，其可傳遞至其他 API，但無法在不轉換內容的情況下在工作流程內部參考。  例如，如果您要傳遞 `application/xml` 內容，可以使用 `@xpath()` 進行 xpath 擷取，或使用 `@json()` to 將 XML 轉換成 JSON。  [這裡提供](../logic-apps/logic-apps-content-type.md)

此外，您可以在定義中指定 JSON 結構描述。 這會讓設計工具產生權杖，接著您可以傳入步驟。  例如，下例會讓設計工具有 `title` 和 `name` 權杖可用︰

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

## <a name="referencing-the-content-of-the-incoming-request"></a>參考連入要求的內容
`@triggerOutputs()` 函數將輸出連入要求的內容。 例如，它應該看起來如下：

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

## <a name="responding-to-the-request"></a>回應要求
對於某些啟動邏輯應用程式的要求，您可能想要使用某些內容來回應呼叫者。 有一個名為 **response** 的新動作類型，可用來建構回應的狀態碼、主體及標頭。 請注意，如果沒有**回應**圖形，則邏輯應用程式端點會*立即*回應 **202 已接受**。

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
| --- | --- |
| StatusCode |要回應連入要求的 HTTP 狀態碼。 它可以是任何以 2xx、4xx 或 5xx 開頭的有效狀態碼。 不允許&3;xx 狀態碼。 |
| body |主體物件可以是字串、JSON 物件，甚至是上一個步驟中所參考的二進位內容。 |
| headers |您可以定義要包含於回應中的標頭，且數目不限 |

邏輯應用程式中針對回應所需的所有步驟都必須在原始要求收到要求的 *60 秒* 內完成，才能接到回應， **除非工作流程已呼叫為巢狀邏輯應用程式**。 如果在 60 秒內未達成任何回應動作，則連入要求將會逾時，並收到 [408 用戶端逾時]  HTTP 回應。  針對巢狀Logic Apps，無論花費多少時間，父邏輯應用程式都會繼續等候回應，直到完成為止。

## <a name="advanced-endpoint-configuration"></a>進階的端點組態
邏輯應用程式內建直接存取端點的支援，並一律使用 `POST` 方法來啟動邏輯應用程式執行。 **HTTP 接聽程式** API 應用程式之前也支援變更 URL 區段和 HTTP 方法。 您甚至可以藉由將其加入 API 應用程式主機 (已裝載 API 應用程式的 Web 應用程式)，來設定額外的安全性或自訂網域。 

這項功能是透過 **API 管理**取得：

* [變更要求的方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [變更要求的 URL 區段](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* 在傳統 Azure 入口網站的 [設定]  索引標籤上設定 API 管理網域
* 設定檢查基本驗證的原則 (**需要的連結**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>從 2014-12-01-preview 開始的移轉摘要
| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| 按一下 [HTTP 接聽程式]  API 應用程式 |按一下 [手動觸發程序]  (不需要 API 應用程式) |
| HTTP 接聽程式設定 [自動傳送回應] |可能包含 **回應** 動作或不在工作流程定義中 |
| 設定基本或 OAuth 驗證 |透過 API 管理 |
| 設定 HTTP 方法 |透過 API 管理 |
| 設定相對路徑 |透過 API 管理 |
| 透過 `@triggerOutputs().body.Content` 參考連入主體 |透過 `@triggerOutputs().body` |
| **傳送 HTTP 回應** 動作 |按一下 [回應 HTTP 要求]  (不需要 API 應用程式) |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

