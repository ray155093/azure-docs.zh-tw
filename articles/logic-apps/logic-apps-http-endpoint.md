---
title: "透過 HTTP 端點呼叫、觸發或巢狀處理邏輯應用程式 - Azure Logic Apps | Microsoft Docs"
description: "在 Azure 中新增並設定 HTTP 端點，以呼叫、觸發或巢狀處理邏輯應用程式的工作流程"
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
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 8c4cb0aefcd7241b17c6afcf33496c1fc2e6f675
ms.lasthandoff: 03/03/2017

---

# <a name="add-http-endpoints-to-call-trigger-or-nest-logic-app-workflows"></a>新增 HTTP 端點以呼叫、觸發或巢狀處理邏輯應用程式的工作流程

邏輯應用程式可以原生方式公開同步的 HTTP 端點做為觸發程序，讓您能夠呼叫邏輯應用程式。 您也可以使用可呼叫端點的模式，藉由將 [選擇 Logic Apps 工作流程] 動作新增到邏輯應用程式中，來叫用邏輯應用程式做為巢狀工作流程。

您可以使用這些觸發程序來接收要求：

* 要求
* ApiConnectionWebhook
* HttpWebhook

本主題使用**要求**觸發程序做為範例，但所有原則同樣適用於其他觸發程序類型。

## <a name="add-a-trigger-to-your-logic-app-definition"></a>將觸發程序新增至邏輯應用程式定義

1. 在邏輯應用程式設計工具中，新增可收到邏輯應用程式定義之連入要求的觸發程序。 例如，將**要求**觸發程序新增至邏輯應用程式。

2.    在 [要求本文 JSON 結構描述] 下方，您可以針對預期要收到的承載輸入一個 JSON 結構描述。 如果您尚未準備好結構描述，可以選擇 [請使用範例承載產生結構描述]，從範例承載中產生 JSON 結構描述。

    設計工具會使用此結構描述來產生權杖，以協助您透過工作流程，從手動觸發程序中取用、剖析和傳遞資料。

    ![新增要求動作][2]

2.    儲存邏輯應用程式定義之後，您將可在 [此 URL 的 HTTP POST] 下方取得產生的回呼 URL，如下列範例所示：

    ``` text
    https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
    ```

    此 URL 的查詢參數中包含用於驗證的共用存取簽章 (SAS) 金鑰。 
    您也可以從 Azure 入口網站取得此端點 ：

    ![URL 端點][1]

    或藉由呼叫：

    ``` text
    POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
    ```

### <a name="change-the-http-method-for-your-trigger"></a>變更觸發程序的 HTTP 方法

根據預設，要求觸發程序會預期 HTTP POST 要求。 若要設定不同的 HTTP 方法，請選擇 [顯示進階選項]。

> [!NOTE]
> 只允許一種方法。

### <a name="customize-the-relative-trigger-url"></a>自訂相對的觸發程序 URL

您也可以自訂要求 URL 的相對路徑來接受參數。

1. 在 [要求] 觸發程序中，選擇 [顯示進階選項]。 在 [相對路徑] 下方，輸入 `customer/{customerId}`。

    ![相對的 URL 觸發程序](./media/logic-apps-http-endpoint/relativeurl.png)

2.    若要使用參數，請更新 [回應] 動作。

    *    您應該會看到 `customerId` 出現在權杖選擇器中。
    *    若要傳回 `Hello {customerId}`，請更新 [回應] 動作的本文。

    ![相對的 URL 回應](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. 儲存您的邏輯應用程式。 系統會使用相對路徑來更新要求 URL。

4. 複製新的要求 URL，並將它貼到新的瀏覽器視窗中。 使用 `123` `{customerId}`，然後按 Enter。

    應會傳回這段文字：`Your customer Id is 123`

### <a name="security-for-the-trigger-url"></a>觸發程序 URL 的安全性

邏輯應用程式回呼 URL 是使用共用存取簽章 (SAS) 安全產生的。 簽章是以查詢參數的形式傳遞，且必須在引發您的邏輯應用程式之前先驗證。 簽章是透過每個邏輯應用程式、觸發程序名稱，以及要執行作業之秘密金鑰的唯一組合所產生。 除非某人具有邏輯應用程式秘密金鑰的存取權，否則他們無法產生有效的簽章。

## <a name="call-your-logic-app-triggers-endpoint"></a>呼叫邏輯應用程式觸發程序的端點

建立觸發程序的端點之後，您可以透過對完整 URL 的 `POST` 來觸發邏輯應用程式。 您可以在本文中包含更多標頭及任何內容。 如果內容的類型是 `application/json`，您就能從要求內部參考屬性。 否則，內容會被視為單一的二進位單位，其可傳遞至其他 API，但無法在不轉換內容的情況下在工作流程內部參考。 例如，如果您要傳遞 `application/xml` 內容，可以使用 `@xpath()` 進行 XPath 擷取，或使用 `@json()` 來將 XML 轉換成 JSON。 深入了解如何[使用內容類型](../logic-apps/logic-apps-content-type.md)。

此外，您也可以在定義中指定 JSON 結構描述。 此結構描述會讓設計工具產生權杖，讓您可以傳入步驟。 例如，下列範例讓 `title` 和 `name` 權杖可在設計工具中使用：

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

## <a name="reference-the-content-from-the-incoming-request"></a>參考來自連入要求的內容

`@triggerOutputs()` 函式會輸出連入要求的內容。 輸出看起來會像這個範例：

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

若要特別存取 `body` 屬性，您可以使用 `@triggerBody()` 捷徑。 

## <a name="respond-to-the-request"></a>回應要求

對於某些啟動邏輯應用程式的要求，您可能想要使用某些內容來回應呼叫者。 若要建構回應的狀態碼、本文及標頭，您可以使用名為**回應**的動作類型。 不過，若未包含**回應**，邏輯應用程式端點就會「立即」回應 [202 已接受]。

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

回應具有下列屬性：

| 屬性 | 說明 |
| --- | --- |
| StatusCode |要回應連入要求的 HTTP 狀態碼。 此代碼可以是任何以 2xx、4xx 或 5xx 開頭的有效狀態碼。 但是，不允許 3xx 狀態碼。 |
| body |主體物件可以是字串、JSON 物件，甚至是上一個步驟中所參考的二進位內容。 |
| headers |您可以定義要包含於回應中的標頭，且數目不限。 |

在您的邏輯應用程式中，除非您呼叫工作流程做為巢狀邏輯應用程式，否則，回應所需的所有步驟必須都在原始要求的「60 秒」內完成，才能取得回應。 如果未在 60 秒內產生任何回應，連入要求便會逾時，並收到 [408 用戶端逾時] HTTP 回應。 針對巢狀邏輯應用程式，無論需要多少時間，父邏輯應用程式都會繼續等候回應，直到完成為止。

## <a name="advanced-endpoint-configuration"></a>進階的端點組態

邏輯應用程式內建直接存取端點的支援，且一律使用 `POST` 方法來啟動邏輯應用程式執行。 **HTTP 接聽程式** API 應用程式之前也支援變更 URL 區段和 HTTP 方法。 您甚至可藉由將這些項目新增至 API 應用程式主機 (已裝載 API 應用程式的 Web 應用程式)，來設定額外的安全性或自訂網域。 

此功能是透過 **API 管理**取得：

* [變更要求方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [變更要求的 URL 區段](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* 在傳統 Azure 入口網站的 [設定] 索引標籤上，設定 API 管理網域。
* 設定檢查基本驗證的原則。

## <a name="summary-of-migration-from-2014-12-01-preview"></a>從 2014-12-01-preview 開始的移轉摘要

| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| 按一下 [HTTP 接聽程式] API 應用程式 |按一下 [手動觸發程序] (不需要 API 應用程式) |
| HTTP 接聽程式設定 [自動傳送回應] |可能包含**回應**動作或不在工作流程定義中。 |
| 設定基本或 OAuth 驗證 |透過 API 管理 |
| 設定 HTTP 方法 |透過 API 管理 |
| 設定相對路徑 |透過 API 管理 |
| 透過 `@triggerOutputs().body.Content` |透過 `@triggerOutputs().body` |
| **傳送 HTTP 回應** 動作 |按一下 [回應 HTTP 要求] (不需要 API 應用程式) |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

