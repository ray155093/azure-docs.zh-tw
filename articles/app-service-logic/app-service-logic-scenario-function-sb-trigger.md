<properties
   pageTitle="邏輯應用程式案例︰Azure Function 服務匯流排觸發程序 | Microsoft Azure"
   description="查看如何使用 Azure Functions 做為 Logic Apps 的服務匯流排觸發程序"
   services="app-service\logic,functions"
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
   ms.date="05/23/2016"
   ms.author="jehollan"/>
   
# 邏輯應用程式案例︰Azure Function 服務匯流排觸發程序

需要部署長時間執行的接聽程式或工作時，Azure Functions 可以做為 Logic Apps 的觸發程序。例如，您可以建立 Azure Function，而該 Azure Function 會在佇列上接聽，並立即引發邏輯應用程式成為推送觸發程序。

## 建置邏輯應用程式

在此範例中，您會有針對需要觸發的每個邏輯應用程式執行的函式。首先您必須使用 HTTP 要求觸發程序建立邏輯應用程式 - 每次收到佇列訊息時，Azure Function 就會呼叫該端點。

1. 開啟新的邏輯應用程式，然後選取 [手動 - 接收 HTTP 要求時] 觸發程序。  
    * 您可以選擇性地指定佇列訊息將透過 [jsonschema.net](http://jsonschema.net) 之類的工具取得的 JSON 結構描述，並將它貼在觸發程序中。這可讓設計工具了解資料的形式，並輕鬆地透過工作流程傳送屬性。
1. 新增您希望在收到佇列訊息後進行的任何步驟。例如，您可以透過 Office 365 傳送電子郵件訊息。  
1. 儲存邏輯應用程式，以產生此邏輯應用程式的觸發程序的回呼 URL。URL 會出現在觸發程序卡上。

![][1]

## 建置 Azure 函式

接著，您必須建立一個 Azure Function，以做為觸發程序並接聽佇列。

1. 開啟 [Azure Functions 入口網站](https://functions.azure.com/signin)，然後選取 [新增函式] 與 [ServiceBusQueueTrigger-C#] 範本。

    ![][2]

2. 設定服務匯流排佇列的連線 (將會使用服務匯流排 SDK `OnMessageReceive()` 接聽程式)
3. 撰寫簡單的函式以呼叫邏輯應用程式端點 (上述) 與佇列訊息。以下是函數的完整範例，其訊息內容類型為 `application/json`，但可以視需要變更。

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

透過[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer)之類的工具新增佇列訊息，即可進行測試，並可看到邏輯應用程式會在函式收到此訊息後立即引發。

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0601_2016-->