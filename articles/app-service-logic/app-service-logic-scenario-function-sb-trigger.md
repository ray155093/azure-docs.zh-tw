---
title: "邏輯應用程式案例︰建立 Azure Functions 服務匯流排觸發程序 | Microsoft Docs"
description: "使用 Azure Functions 建立邏輯應用程式的服務匯流排觸發程序"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3f8031d31d1291972026430e67005863579ae911


---
# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>邏輯應用程式案例︰使用 Azure Functions 建立 Azure 服務匯流排觸發程序
當您需要部署長時間執行的接聽程式或工作時，可以使用 Azure Functions 來建立邏輯應用程式的觸發程序。 例如，您可以建立一個將會在佇列上接聽的函數，接著立即引發邏輯應用程式成為推送觸發程序。

## <a name="build-the-logic-app"></a>建置邏輯應用程式
在此範例中，您會有針對需要觸發的每個邏輯應用程式執行的函數。 首先，建立具有 HTTP 要求觸發程序的邏輯應用程式。 每當收到佇列訊息時，函數即會呼叫該端點。  

1. 建立新的邏輯應用程式；選取 [手動 - 收到 HTTP 要求時]  觸發程序。  
   您可以選擇性地利用 [jsonschema.net](http://jsonschema.net)之類的工具，來指定要與佇列訊息搭配使用的 JSON 結構描述。 在觸發程序中貼上結構描述。 這有助於讓設計工具了解資料的形式，而且可以更輕鬆地透過工作流程傳送屬性。
2. 新增您想要在收到佇列訊息之後發生的任何其他步驟。 例如，透過 Office 365 傳送電子郵件。  
3. 儲存邏輯應用程式，以產生此邏輯應用程式的觸發程序的回呼 URL。 URL 會出現在觸發程序卡上。

![回呼 URL 會出現在觸發程序卡上][1]

## <a name="build-the-function"></a>建置函數
接著，您必須建立一個函數，以做為觸發程序並接聽佇列。

1. 在 [Azure Functions 入口網站](https://functions.azure.com/signin)中，依序選取 [新增函數] 與 [ServiceBusQueueTrigger - C#] 範本。
   
    ![Azure Functions 入口網站][2]
2. 設定服務匯流排佇列的連接 (將使用服務匯流排 SDK `OnMessageReceive()` 接聽程式)。
3. 撰寫簡單的函數，使用佇列訊息做為觸發程序來呼叫邏輯應用程式端點 (稍早建立的)。 以下是函數的完整範例。 此範例會使用 `application/json` 訊息內容類型，但是您可以視需要變更此類型。
   
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

若要進行測試，請透過 [服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer)之類的工具來新增佇列訊息。 請查看在函數收到訊息之後立即引發的邏輯應用程式。

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG



<!--HONumber=Nov16_HO3-->


