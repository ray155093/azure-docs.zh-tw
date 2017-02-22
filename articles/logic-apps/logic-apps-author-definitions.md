---
title: "撰寫邏輯應用程式定義 | Microsoft Docs"
description: "了解如何撰寫邏輯應用程式的 JSON 定義"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 08b59b7aaa28339c4168e736105fdbe7295f5255


---
# <a name="author-logic-app-definitions"></a>撰寫邏輯應用程式定義
本主題示範如何使用 [Azure Logic App](logic-apps-what-are-logic-apps.md) 定義，這是一種簡單的宣告式 JSON 語言。 請先看看 [如何建立新的邏輯應用程式](logic-apps-create-a-logic-app.md) (如果還沒看過)。 您也可以閱讀 [MSDN 上關於此定義語言的完整參考資料](http://aka.ms/logicappsdocs)。

## <a name="several-steps-that-repeat-over-a-list"></a>清單上重複的幾個步驟
您可以利用 [foreach 類型](logic-apps-loops-and-scopes.md) 逐一執行由最多 10,000 個項目所組成的陣列，並針對每個項目執行動作。

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>發生錯誤時的失敗處理步驟
您通常想要撰寫*補救步驟* — 如果**且唯有**當一或多個呼叫失敗時執行的一些邏輯。 在此範例中，我們從各種地方取得資料，但如果呼叫失敗，我想要在某處 POST 訊息，方便稍後追蹤該失敗。：  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

您可以使用 `runAfter` 屬性來指定 `postToErrorMessageQueue` 只在 `readData` 是 **Failed** 之後才應該執行。  這也可以是可能值清單，因此 `runAfter` 可以是 `["Succeeded", "Failed"]`。

最後，因為您現在已經處理錯誤，我們不再將執行結果標示為 **失敗**。 您在這裡可以看到，即使一個步驟失敗，此執行也 **成功** ，因為我撰寫步驟來處理這項失敗。

## <a name="two-or-more-steps-that-execute-in-parallel"></a>平行執行的兩個以上步驟
若要平行執行多個動作， `runAfter` 屬性在執行階段必須相同。 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

如以上範例所示，`branch1` 和 `branch2` 皆設定為在 `readData` 之後執行。 因此，這兩個分支會平行執行：

![平行](media/logic-apps-author-definitions/parallel.png)

您可以看到兩個分支的時間戳記完全相同。 

## <a name="join-two-parallel-branches"></a>聯結兩個平行分支
透過類似上述方式在 `runAfter` 屬性新增項目，即可聯結設定為平行執行的兩個動作。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![平行](media/logic-apps-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>將清單中的項目對應至一些不同的組態
接下來，假設我們想要根據屬性的值取得完全不同的內容。 我們可以建立值與目的地的對應做為參數：  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

在此案例中，我們先取得文章清單，然後第二個步驟根據已定義為參數的類別，在對應中查詢可取得內容的 URL。 

在此要注意兩個項目： [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) 函式用來檢查類別是否符合其中一個已定義的已知類別。 其次，一旦得到類別，我們可以使用方括號提取對應的項目： `parameters[...]`。 

## <a name="working-with-strings"></a>處理字串
我們提供了各種可用來處理字串的函式。 我們來看一個範例，假設我們想要將一個字串傳遞到系統，但不確定字元編碼是否會正確處理。 一種作法是以 base64 將此字串編碼。 不過，為了避免在 URL 中逸出，我們要取代幾個字元。 

我們也想要訂單名稱的子字串，因為不會用到前 5 個字元。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

詳細作法：

1. 取得訂購者名稱的 [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length)，這會傳回字元總數
2. 減 5 (因為我們要較短的字串)
3. 實際取得 [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) 。 我們從索引 `5` 開始，並取得字串的其餘部分。
4. 將這個子字串轉換成 [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) 字串
5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 所有 `+` 字元換成 `-`
6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 所有 `/` 字元換成 `_`

## <a name="working-with-date-times"></a>使用日期時間
日期時間很有用，特別是在嘗試從不支援的 **觸發程序**的資料來源提取資料時。  您也可以使用日期時間算出各步驟花費的時間。 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

在此範例中，我們擷取前一個步驟的 `startTime` 。 然後，我們取得目前的時間並減去一秒：[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (您可以使用其他的時間單位，例如 `minutes` 或 `hours`)。 最後，我們可以比較這兩個值。 如果第一個值小於第二個值，即表示自從訂單最初提交以來已超過一秒。 

也請注意，我們可以使用字串格式子來格式化日期：我在查詢字串中使用 [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) 取得 RFC1123。 所有日期格式 [記載於 MSDN 上](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)。 

## <a name="using-deployment-time-parameters-for-different-environments"></a>對不同的環境使用部署階段參數
部署生命週期中通常會有開發環境、預備環境及生產環境。 在所有這些環境中，舉例來說，您可能想要有相同的定義，但使用不同的資料庫。 同樣地，您可能想要跨許多不同的區域使用相同的定義，以發揮高可用性，但希望每個邏輯應用程式執行個體與該區域資料庫互動。 

請注意，這不同於在「執行階段」採用不同的參數，對此您應該使用上述的 `trigger()` 函式。 

您可以從非常簡單的定義開始，如下所示：

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

然後，在邏輯應用程式的實際 `PUT` 要求中，您可以提供參數 `uri`。 請注意，由於已不再有預設值，邏輯應用程式內容中需要這個參數：

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

然後，在每個環境中，您就可以提供不同的值給 `connection` 參數。 

如需有關建立及管理邏輯應用程式的所有可用選項，請參閱 [REST API 文件](https://msdn.microsoft.com/library/azure/mt643787.aspx) 。 




<!--HONumber=Jan17_HO3-->


