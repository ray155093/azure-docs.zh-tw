<properties
   pageTitle="Logic Apps 迴圈、範圍和解除批次處理 | Microsoft Azure"
   description="邏輯應用程式迴圈、範圍和解除批次處理的概念"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# Logic Apps 迴圈、範圍和解除批次處理
  
>[AZURE.NOTE] 這一版的文章適用於 Logic Apps 2016-04-01-preview 結構描述和更新版本。概念與較舊的結構描述很類似，但範圍僅適用於這個結構描述和更新版本。
  
## ForEach 迴圈和陣列
  
Logic Apps 可讓您在一組資料上進行迴圈，並為每個項目執行動作。這可透過 `foreach` 動作來達成。在設計工具中，您可以指定要新增一個 for each 迴圈。在選取您希望反覆查看的陣列之後，就可以新增動作。目前您受限於每個 foreach 迴圈只能有一個動作，但在未來幾週內，將會提高此限制。一旦進入迴圈之後，您就可以開始指定應針對陣列的每個值執行哪些動作。

如果使用程式碼檢視，您就可以指定 for each 迴圈，如下所示。這是 for each 迴圈的範例，可針對每個包含「microsoft.com」的電子郵件地址傳送傳送電子郵件︰

```
{
    "forEach_email": {
        "type": "foreach",
        "foreach": "@triggerBody()['emails']",
        "expression": "@contains(item(), 'microsoft.com')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        }
    }
}
```
  
  `foreach` 動作可以反覆查看陣列，最多 5,000 個資料列。每個反覆項目都可平行執行，因此可能需要視流量控制來將訊息新增至佇列。
  
## Until 迴圈
  
  您可以執行某個動作或一系列動作，直到符合某個條件為止。對於此迴圈的最常見案例是呼叫端點，直到您取得所需的回應為止。在設計工具中，您可以指定要新增一個 until 迴圈。在迴圈中新增動作之後，您就可以設定結束條件以及迴圈限制。迴圈循環之間有 1 分鐘的延遲。
  
  如果使用程式碼檢視，您可以指定 until 迴圈，如下所示。這個範例會呼叫 HTTP 端點，直到回應主體具有「已完成」的值為止。它將會在符合下列其中一種情況時完成
  
  * HTTP 回應具有「已完成」的狀態
  * 已嘗試 1 小時的時間
  * 已執行過 100 次的迴圈
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## SplitOn 和解除批次處理

觸發程序有時可能會接收到您想要解除批次處理並針對每個項目啟動工作流程的項目陣列。這可透過 `spliton` 命令來完成。根據預設，如果您的觸發程序 Swagger 指定的承載是一個陣列，即會新增 `spliton`，並針對每個項目開始執行。SplitOn 只會新增至觸發程序。這可以在定義程式碼檢視中手動設定或覆寫。SplitOn 目前可以解除批次處理陣列，最多 5,000 個項目。您無法具有 `spliton` 並同時實作同步回應模式。若所呼叫的任何工作流程除了 `spliton` 之外還有 `response` 動作，將會以非同步方式執行並傳送立即 `202 Accepted` 回應。

SplitOn 可以指定於程式碼檢視中，如下列範例所示。這將會接收到項目的陣列，並在每一列上解除批次處理。

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## 範圍

可能可以使用 scope 來將一系列動作群組在一起。這特別適合用來實作例外狀況處理。在設計工具中，您可以新增範圍，並開始在其內部新增任何動作。您可以在程式碼檢視中定義範圍，如下所示︰


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

<!---HONumber=AcomDC_0601_2016-->