---
title: 搭配使用 Azure Functions 與 Logic Apps | Microsoft Docs
description: 查看搭配使用 Azure Functions 與 Logic Apps 的方法
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/01/2016
ms.author: jehollan

---
# 搭配使用 Azure Functions 與 Logic Apps
您可以從邏輯應用程式內使用 Azure Functions，來執行 C# 或 node.js 的自訂程式碼片段。[Azure Functions](../azure-functions/functions-overview.md) 提供 Microsoft Azure 中無伺服器運算的功能。這對於執行下列工作很有幫助：

* 格式化動作的值 (例如，從日期時間轉換為日期字串)
* 執行工作流程內的計算
* 利用 C# 或 node.js 中支援的函數來擴充 Logic Apps 的功能

## 建立 Logic Apps 的函數
建議您在 Azure Functions 入口網站使用**一般 Webhook - 節點**或**一般 Webhook - C#** 範本來建立新的 Azure Functions。這將會自動填入可接受來自邏輯應用程式之 `application/json` 的範本。將會自動探索使用這些範本的函數，並列於 [我的區域中的 Azure Functions] 下方的 Logic Apps 設計工具中。

Webhook 函數會接受要求，並透過 `data` 變數將它傳入方法。您可以使用點標記法 (例如 `data.foo`) 來存取承載的屬性。例如，將日期時間值轉換為日期字串的簡單 JavaScript 函數看起來如以下範例︰

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## 從邏輯應用程式呼叫 Azure Functions
在設計工具中，如果您按一下 [動作] 功能表，就能選取 [我的區域中的 Azure Functions]。這會列出您訂用帳戶中的容器，並可讓您選擇想要呼叫的函數。

選取函數之後，系統會提示您指定輸入承載物件。這是邏輯應用程式將傳送到函數的訊息，且必須是 JSON 物件。例如，如果我想要傳入 Salesforce 觸發程式的**上次修改**日期，函數承載可能看起來如下︰

![上次修改日期][1]

## 從函數觸發 Logic Apps
此外，也可以從函數內觸發邏輯應用程式。若要這樣做，只需使用手動觸發程序來建立邏輯應用程式即可。如需詳細資訊，請參閱[作為可呼叫端點的 Logic Apps](app-service-logic-http-endpoint.md)。接著在您的函數內，產生 HTTP POST 到手動觸發程序 URL，其中包含您想要傳送到邏輯應用程式的承載。

### 從設計工具建立函數
您也可以從設計工具內建立 node.js webhook 函數。首先，選取 [我的區域中的 Azure Functions]，然後選擇適用於您的函數的容器。如果您還沒有容器，就必須從 [Azure Functions 入口網站](https://functions.azure.com/signin)建立一個。然後選取 [建立新的)。

若要根據您想要計算的資料來產生範本，請指定您打算傳入函數的內容物件。這必須是 JSON 物件。例如，如果您從 FTP 動作傳入檔案內容，內容承載看起來會像這樣︰

![內容承載][2]

> [!NOTE]
> 因為此物件無法轉換為字串，所以內容會直接新增至 JSON 承載中。不過，如果它不是 JSON 權杖 (也就是字串或 JSON 物件/陣列)，將會發生錯誤。若要將其轉換為字串，只要加上引號，如在本文章的第一個圖例所示。
> 
> 

設計工具接著會產生您可以內嵌建立的函數範本。變數會根據您想要傳入函數的內容預先建立。

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!----HONumber=AcomDC_0907_2016-->