<properties
   pageTitle="Logic Apps 例外狀況處理 | Microsoft Azure"
   description="了解 Azure Logic Apps 的錯誤和例外狀況處理模式"
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

# Logic Apps 的錯誤和例外狀況處理

Logic Apps 提供了一組豐富的工具和模式，以協助確保您的整合穩固且具有彈性，不怕故障。任何整合架構都會面臨挑戰，其中之一便是要如何確保相依系統的停機或問題會受到妥善處理。Logic Apps 讓處理錯誤成為一種頂級享受，賦予您所需工具來處理工作流程內的例外狀況和錯誤。

## 重試原則

最基本的例外狀況和錯誤處理類型是重試原則。此原則會定義如果初始要求逾時或失敗 (任何造成 429 或 5xx 回應的要求)，是否應該重試動作。根據預設，所有動作會在 20 秒的間隔內另外再試 3 次。因此，如果第一個要求收到 `500 Internal Server Error` 回應，工作流程引擎就會暫停 20 秒，然後再一次嘗試要求。如果在經過所有重試後回應仍是例外狀況或失敗，工作流程將會繼續，並將動作狀態標示為 `Failed`。

您可以在特定動作的**輸入**中設定重試原則。重試原則可以設定為在 1 小時的間隔內嘗試多達 4 次。輸入屬性的完整詳細資料可以[在 MSDN 上找到][retryPolicyMSDN]。

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

如果您想要 HTTP 動作重試 4 次，並在每次嘗試之間等候 10 分鐘，您必須進行下列定義︰

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

如需所支援語法的詳細資訊，請檢視 [MSDN 上的重試原則區段][retryPolicyMSDN]。

## 用來擷取失敗的 RunAfter 屬性

每個邏輯應用程式動作都會宣告要讓其啟動所必須先完成的那些動作。您可以將此視為工作流程的步驟順序。在動作定義中，此順序稱為 `runAfter` 屬性。它是會說明哪些動作和動作狀態會執行此動作的物件。根據預設，所有透過設計工具新增的動作都會設定為如果上一個步驟 `Succeeded`，則在上一個步驟之後執行 (`runAfter`)。不過，您可以自訂此值，以在先前的動作是 `Failed`、`Skipped`或這兩個值的可能組合時引發動作。如果您想要在特定動作 `Insert_Row` 失敗之後於指定的服務匯流排主題新增項目，您可以使用下列 `runAfter` 組態︰

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ "content-type" : "application/json" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

請注意，`runAfter` 屬性會設定為在 `Insert_Row` 動作是 `Failed` 時引發。若要在動作狀態是 `Succeeded`、`Failed` 或 `Skipped` 時執行動作，語法會是︰

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] 前一個動作失敗之後所執行的動作會在順利完成後標示為 `Succeeded`。此行為表示如果您成功擷取到工作流程中的所有失敗，該次執行本身會標示為 `Succeeded`。

## 用以評估動作的範圍和結果

和您可以在個別動作之後進行的執行方式類似，您也可以將多個動作群組到某個[範圍](app-service-logic-loops-and-scopes.md)內 - 其可做為這些動作的邏輯群組。範圍適合用來組織邏輯應用程式動作，以及用來對某一範圍的狀態執行彙總評估。範圍本身會在範圍內的所有動作完成之後收到狀態。範圍狀態會使用和執行 (run) 相同的準則來做決定 - 如果執行 (execution) 分支中的最後一個動作是 `Failed` 或 `Aborted`，狀態會是 `Failed`。

您可以在範圍標示為 `Failed` 之後執行 (`runAfter`)，以針對範圍內發生的任何失敗引發特定動作。在範圍失敗之後執行可讓您建立一項動作，以在範圍內的「任何」動作失敗時擷取失敗。

### 透過結果取得失敗的內容

從範圍擷取失敗非常實用，但您可能也想要取得內容以了解實際上有哪些動作失敗，以及所傳回的任何錯誤或狀態碼。`@result()` 工作流程函式會將內容提供到範圍內所有動作的結果中。

`@result()` 採用單一參數、範圍名稱，並且會傳回該範圍內產生之所有動作的陣列。這些動作物件包含和 `@actions()` 物件相同的屬性，包括動作開始時間、動作結束時間、動作狀態、動作輸入、動作相互關聯識別碼和動作輸出。您可以輕易地配對 `@result()` 函式與 `runAfter`，以傳送在範圍內失敗之任何動作的內容。

如果您想要對範圍中的「每個」`Failed`動作執行動作，您可以配對 `@result()` 與**[篩選陣列](../connectors/connectors-native-query.md)**動作和 **[ForEach](app-service-logic-loops-and-scopes.md)** 迴圈。這可讓您篩選出結果陣列中已失敗的動作。您可以取得篩選後的結果陣列，並使用 **ForEach** 迴圈對每個失敗執行動作。我們在下面提供了範例，並會接著提供詳細說明。這個範例會傳送 HTTP POST 要求，其中含有任何在範圍 `My_Scope` 內失敗之動作的回應本文。

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

以下是整個過程的詳細逐步解說︰

1. **篩選陣列**動作篩選 `@result('My_Scope')` 以取得 `My_Scope` 內所有動作的結果
1. **篩選陣列**的條件是狀態等於 `Failed` 的任何 `@result()` 項目。這會將來自 `My_Scope` 之所有動作結果的陣列篩選到只剩失敗動作結果的陣列。
1. 對**篩選後陣列**的輸出執行 **For Each** 動作。這會針對我們在上面篩選出的「每個」失敗動作結果執行動作。
    - 如果在範圍內只有一個動作失敗，`foreach` 中的動作只會執行一次。許多失敗動作會對每個失敗造成一個動作。
1. 在 `foreach` 項目的回應本文上傳送 HTTP POST，或傳送 `@item()['outputs']['body']`。`@result()` 項目圖形和 `@actions()` 圖形相同，並可透過相同方式剖析。
1. 另外也包含了兩個具有失敗動作名稱 `@item()['name']` 和失敗執行用戶端追蹤識別碼 `@item()['clientTrackingId']` 的自訂標頭。

以下提供了單一 `@result()` 項目的範例供您參考。您可以看到 `name`、`body` 和 `clientTrackingId` 屬性已在上述範例中剖析。請注意，在 `foreach` 之外，`@result()` 會傳回這些物件的陣列。

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

您可以使用上述運算式來執行不同的例外狀況處理模式。您可以選擇在範圍之外執行單一的例外狀況處理動作，以接受整個篩選後的失敗陣列並移除 `foreach`。您也可以包含上述 `@result()` 回應中的其他有用屬性。

## Azure 診斷和遙測

上述模式非常適合處理執行內的錯誤和例外狀況，但您也可以識別和回應與執行本身無關的錯誤。[Azure 診斷](app-service-logic-monitor-your-logic-apps.md)提供了簡單的方法讓您將所有工作流程事件 (包括所有執行和動作狀態) 傳送至 Azure 儲存體帳戶或 Azure 事件中樞。您可以監視記錄檔和度量，或將它們發佈至您偏好使用的任何監視工具以評估執行狀態。其中一個可能的選項是透過 Azure 事件中樞將所有事件串流到[串流分析](https://azure.microsoft.com/services/stream-analytics/)。在串流分析中，您可以將診斷記錄中任何異常、平均或失敗的即時查詢取消。串流分析可以輕鬆地輸出至其他資料來源，例如佇列、主題、SQL、DocumentDB 和 Power BI。

## 後續步驟
- [看看某位客戶如何使用 Logic Apps 建置健全的錯誤處理](app-service-logic-scenario-error-and-exception-handling.md)
- [尋找其他 Logic Apps 範例和案例](app-service-logic-examples-and-scenarios.md)
- [了解如何建立邏輯應用程式的自動化部署](app-service-logic-create-deploy-template.md)
- [在 Visual Studio 中設計和部署邏輯應用程式](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9

<!---HONumber=AcomDC_0817_2016-->