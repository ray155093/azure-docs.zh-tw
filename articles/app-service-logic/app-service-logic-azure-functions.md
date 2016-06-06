<properties
   pageTitle="搭配 Logic Apps 使用 Azure Functions | Microsoft Azure"
   description="查看搭配 Logic Apps 使用 Azure Functions 的方法"
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
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# 搭配 Logic Apps 使用 Azure Functions

您可以利用 Azure Functions 搭配邏輯應用程式，來執行 C# 或 Node.js 的自訂程式碼片段。[Azure Functions](../azure-functions/functions-overview.md) 是一種服務，允許在 Microsoft Azure 中進行無伺服器的計算。在 Logic Apps 內，針對下列許多案例而言，這非常實用：

* 格式化動作的值 (例如，從日期時間轉換為日期字串)
* 執行工作流程內的計算
* 利用 C# 或 Node.js 中支援的函數來擴充 Logic Apps 功能

## 建立適用於 Logic Apps 的 Azure 函數

建議您在函數入口網站使用「一般節點 Webhook」或「一般 C# Webhook」範本來建立新的 Azure Function。這將會自動填入可接受來自邏輯應用程式之 `application/json` 的範本，而且將會自動探索使用這些範本的函數，並列於 [我的區域中的 Azure Functions] 下方的 Logic Apps 設計工具中。

Webhook 函數會接受要求，並透過 `data` 變數將它傳入方法。您可以使用點標記法 (例如 `data.foo`) 來存取承載的屬性。例如，將日期時間值轉換為日期字串的簡單 javascript 函數看起來如下︰

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## 從邏輯應用程式呼叫 Azure Functions

在設計工具中，如果您針對動作按一下下拉式功能表，就能選取 [我的區域中的 Azure Functions]。 這將會列出您訂用帳戶中的容器，並可讓您選擇想要呼叫的函數。選取之後，系統會提示您指定輸入承載物件。這是邏輯應用程式將傳送到函數的訊息，且必須是 JSON 物件。例如，如果我想要傳入 Salesforce 觸發程式的「上次修改」日期，函數承載可能看起來如下︰

![][1]

## 從 Azure 函數觸發 Logic Apps

此外，也可以從函數內觸發邏輯應用程式。若要這樣做，只需使用手動觸發程序來建立邏輯應用程式即可 (詳細資料請參閱[這裡](app-service-logic-http-endpoint.md))。接著在您的 Azure 函數內，產生 HTTP POST 到手動觸發程序 URL，其中包含您想要傳送到邏輯應用程式的承載。

### 從設計工具建立函數

您也可以從設計工具內建立 node.js webhook 函數。首先，選取 [我的區域中的 Azure Functions]，然後選擇適用於您的函數的容器。如果您還沒有容器，就必須從 [Azure Functions 網站](https://functions.azure.com/signin)建立一個。接著，您可以選取 [建立新的]。 若要根據您想要計算的資料來產生範本，請指定您打算傳入函數的內容物件。這必須是 JSON 物件。例如，如果我從 FTP 動作傳入檔案內容，我內容承載看起來會像這樣︰

![][2]

>[AZURE.NOTE] 因為此物件無法藉由新增引號來轉換為字串，所以內容會直接新增至 JSON 承載中。如果它不是 JSON 權杖 (也就是字串或 JSON 物件/陣列)，將會發生錯誤。若要轉換為字串，只要在上述 Salesforce 範例中新增引號即可。

設計工具接著會建立產生您可以建立內嵌的函數範本。變數會根據您想要傳入函數的內容預先建立。

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0525_2016-->