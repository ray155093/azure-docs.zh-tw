<properties 
	pageTitle="建立 Logic Apps 的 API" 
	description="建立自訂 API 來與 Logic Apps 搭配使用" 
	authors="jeffhollan" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="04/05/2016"
	ms.author="jehollan"/>
    
# 建立自訂 API 來與 Logic Apps 搭配使用

如果您想要擴充 Logic Apps 平台，有許多方法可供您呼叫到 API 或不屬於我們諸多現成連接器之一的系統之中。在這些方法之中，其中一種便是建立可從邏輯應用程式工作流程內呼叫的 API 應用程式。

## 實用工具

為了讓 API 與 Logic Apps 在搭配運作時達到最佳效能，建議您產生 [Swagger](http://swagger.io) 文件來詳述您的 API 所支援的作業和參數。有許多程式庫 (例如 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) 可為您自動產生 Swagger。您也可以使用 [TRex](https://github.com/nihaue/TRex) 來協助您註解 Swagger 以使其與 Logic Apps 良好配合 (顯示名稱、屬性類型等)。如需針對 Logic Apps 所建置之 API Apps 的部分範例，請務必查看我們的 [GitHub 儲存機制](http://github.com/logicappsio)或[部落格](http://aka.ms/logicappsblog)。

## 動作

邏輯應用程式的基本動作是會接受 HTTP 要求並傳回回應 (通常是 200) 的控制器。不過，也有不同的模式可供遵循，以便根據您的需求擴充動作。

根據預設，邏輯應用程式引擎會在 1 分鐘後讓要求逾時。不過，透過遵循下面會詳細說明的非同步或 Webhook 模式，您也可以讓您的 API 執行需要更長時間的動作並讓引擎等候動作完成。

### 長時間執行的動作 - 非同步模式

在執行需要長時間的步驟或工作時，首要工作是確定引擎知道您還沒有逾時。您也需要與引擎溝通它要如何知道您已完成工作，最後，您需要將相關資料傳回給引擎，以便它能繼續工作流程。您可以遵循下列流程以透過 API 來完成。下列步驟是從自訂 API 的觀點出發︰

1\. 收到要求時，立即傳回回應 (再完成工作)。這個回應會是 `202 ACCEPTED` 回應，可讓引擎知道您已收到資料並接受裝載，現在正在處理。202 回應應包含下列標頭︰
 * `location` 標頭 (必要)︰這是可用來檢查作業狀態之 URL Logic Apps 的絕對路徑。
 * `retry-after` (選用，針對動作會預設為 20)。這是引擎在輪詢位置標頭 URL 以檢查狀態前應該等候的秒數。

2\. 在檢查作業狀態時，請執行下列檢查︰
 * 如果作業完成︰傳回 `200 OK` 回應，並具有回應裝載。
 * 如果作業仍在處理︰傳回另一個 `202 ACCEPTED` 回應，並以相同的標頭做為初始回應

此模式可讓您在自訂 API 的執行緒內執行需要相當長時間的工作，但讓 Logic Apps 中的作用中連線保持運作，以免它在工作完成之前逾時或繼續。在將此模式加入邏輯應用程式時，請務必注意您的定義中不需要任何資料，就可讓邏輯應用程式繼續輪詢和檢查狀態。引擎一看到「202 已接受」回應和有效的位置標頭就會採用非同步模式，並繼續輪詢位置標頭直到傳回非 202 為止。

您可以在[這裡](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)查看這種模式在 GitHub 中的範例

### Webhook 動作

在您的工作流程期間，您可以讓邏輯應用程式暫停並等候「回呼」後再繼續。此回呼的形式是 HTTP POST。若要實作此模式，您需要在控制器上提供兩個端點︰訂閱和取消訂閱。

在「訂閱」上，邏輯應用程式會建立並註冊回呼 URL，以供您的 API 準備好以 HTTP POST 的形式儲存和回呼。任何內容/標頭都會傳遞至邏輯應用程式，並可用於工作流程的其餘部分。邏輯應用程式引擎一旦在執行時碰到該步驟，就會呼叫訂閱點。

如果執行遭到取消，邏輯應用程式引擎便會呼叫「取消訂閱」端點。然後您的 API 就可以視需要取消註冊回呼 URL。

邏輯應用程式設計工具目前不支援透過 Swagger 探索 Webhook 端點，因此若要使用這種類型的動作，您必須新增「Webhook」動作並指定 URL、標頭和要求本文。您可以視需要在這些欄位中的任何一個使用 `@listCallbackUrl()` 工作流程函式，以傳遞回呼 URL。

您可以在[這裡](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)查看 Webhook 模式在 GitHub 中的範例

## 觸發程序

除了動作之外，您也可以讓您的自訂 API 做為邏輯應用程式的觸發程序。下面有兩種模式可供遵循以便觸發邏輯應用程式︰

### 輪詢觸發程序

輪詢觸發程序的作用很像上面的長時間執行非同步動作。邏輯應用程式引擎會在經過一段特定時間後呼叫觸發程序端點 (取決於 SKU，「進階」為 15 秒、「標準」為 1 分鐘，「免費」則為 1 小時)。

如果沒有資料可用，觸發程序會傳回 `202 ACCEPTED` 回應以及 `location` 和 `retry-after` 標頭。不過，建議讓觸發程序的 `location` 標頭包含 `triggerState` 的查詢參數。這是可讓您的 API 知道上一次引發邏輯應用程式之時間的少許識別碼。如果有資料可用，觸發程序會傳回 `200 OK` 回應和內容裝載。這將會引發邏輯應用程式。

比方說，如果我進行輪詢以查看檔案是否可用，則您可以建置輪詢觸發程序來執行下列作業︰

* 如果收到的要求沒有 triggerState，API 將會傳回 `202 ACCEPTED` 和 `location` 標頭，且其 triggerState 為目前時間、`retry-after` 為 15。
* 如果收到的要求有 triggerState︰
 * 檢查看看在 triggerState DateTime 後是否新增了任何檔案。 
  * 如果有 1 個檔案，則傳回 `200 OK` 回應和內容裝載、將 triggerState 增加為我傳回之檔案的 DateTime，並將 `retry-after` 設定為 15。
  * 如果有多個檔案，我可以一次傳回 1 個檔案和 `200 OK`、在 `location` 標頭中增加我的 triggerState，並將 `retry-after` 設定為 0。這可讓引擎知道還有其他資料可用，並立即在指定的 `location` 標頭中要求資料。
  * 如果沒有任何可用的檔案，則傳回 `202 ACCEPTED` 回應，並讓 `location` triggerState 保持相同。將 `retry-after` 設定為 15。

您可以在[這裡](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)查看輪詢觸發程序在 GitHub 中的範例

### WebHook 觸發程序

Webhook 觸發程序的作用很像上面的 Webhook 動作。每次新增和儲存 Webhook 觸發程序時，邏輯應用程式引擎便會呼叫「訂閱」端點。每當有資料可用時，您的 API 可以註冊 Webhook URL 並透過 HTTP POST 來加以呼叫。內容裝載和標頭會傳遞至邏輯應用程式執行。

如果 Webhook 觸發程序曾遭到刪除 (不論是刪除整個邏輯應用程式還是只刪除 Webhook 觸發程序)，引擎便會呼叫「取消訂閱」URL 以供您的 API 取消註冊回呼 URL，並視需要停止任何處理程序。

邏輯應用程式設計工具目前不支援透過 Swagger 探索 Webhook 觸發程序，因此若要使用這種類型的動作，您必須新增 "Webhook" 觸發程序並指定 URL、標頭和要求本文。您可以視需要在這些欄位中的任何一個使用 `@listCallbackUrl()` 工作流程函式，以傳遞回呼 URL。

您可以在[這裡](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)查看 Webhook 觸發程序在 GitHub 中的範例

<!---HONumber=AcomDC_0420_2016-->