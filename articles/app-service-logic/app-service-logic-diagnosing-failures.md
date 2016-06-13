<properties
   pageTitle="Logic Apps 診斷應用程式 | Microsoft Azure"
   description="了解常見的方法，以了解應用程式失敗的位置"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/18/2016"
   ms.author="jehollan"/>
   
# 診斷 Logic Apps

如果邏輯應用程式發生任何問題或失敗，有幾種方法可讓您進一步了解失敗原因。

## 管理入口網站工具

Azure 入口網站提供許多工具來診斷每個步驟的每個邏輯應用程式。

### 觸發程序歷程記錄

每個邏輯應用程式有至少一個觸發程序。如果您注意到應用程式並未引發，請先從觸發程序歷程記錄開始。您可以從以下的邏輯應用程式主要刀鋒視窗存取觸發程序歷程記錄︰

![][1]

這會列出您的邏輯應用程式進行的所有觸發嘗試。按一下其中的每個項目，即可取得下一個層級的詳細資料 (特別是觸發程序嘗試所產生的任何輸入或輸出)。如果您發現任何「失敗」觸發程序，您應該按一下觸發程序嘗試並切入至 [輸出連結]，以了解任何可能產生的錯誤訊息 (例如︰FTP 認證無效)。

您可能會看到不同的狀態︰

* 略過 - 它會輪詢端點，查看資料是否可用，並得到沒有資料可用的回應。
* 成功 - 觸發程序收到資料可用的回應。這可能是來自手動觸發程序、循環觸發程序或輪詢觸發程序。這可能會伴隨著「引發」，但如果程式碼檢視中有一個條件或 splitOn 不符合，則可能不會出現。
* 失敗 - 已產生錯誤。

#### 手動啟動觸發程序

如果您要邏輯應用程式立即檢查可用的觸發程序 (而不需等待下一個循環)，可以隨時按一下主要刀鋒視窗上的 [選取觸發程序] 按鈕來強制進行檢查。例如，按一下包含 Dropbox 觸發程序的這個按鈕會導致工作流程立即輪詢 Dropbox 中是否有新檔案。

### 執行歷程記錄

每當觸發程序引發時，它就會造成一次執行。執行可以從主要刀鋒視窗進行存取，而且包含大量有助於了解流程期間發生什麼事情的資訊。

![][2]

執行可以有下列任何狀態︰

* 成功 - 所有動作都成功，或如果發生稍後在工作流程中由某個動作進行處理的失敗 (也就是已設定在「失敗」動作之後執行某個動作)。
* 失敗 - 至少有一個動作發生稍後在工作流程中未由某個動作進行處理的失敗。
* 已取消 - 工作流程執行中，但收到取消要求。
* 執行中 - 如果工作流程目前正在執行中。這可能會出現於正在進行節流的流程以及目前的 App Service 方案。如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/app-service/plans/)上的動作限制。設定診斷 (執行歷程記錄之下的圖表) 也可讓您知道目前發生的任何節流事件。

當您在執行中，您可以進一步探索更多詳細資料。

#### 觸發程序輸出

「觸發程序輸出」會顯示從觸發程序接收的資料。如果所有屬性都如預期般傳回，這可能有助於了解。

>[AZURE.NOTE] 如果您看見任何不了解的內容，這可能有助於了解 Logic Apps 如何[處理不同的內容類型](app-service-logic-content-type.md)。

![][3]

#### 動作輸入和輸出

您可以深入探索動作所接收的輸入和輸出。這有助於了解輸出的大小和圖形，以及查看任何可能產生的錯誤訊息。

![][4]

## 偵錯工作流程執行階段

除了監視執行的輸入、輸出和觸發程序以外，在工作流程內加入一些步驟，有助於偵錯。[RequestBin](http://requestb.in) 是一個強大的工具，您可以在工作流程中將其加入為一個步驟。RequestBin 可讓您設定 HTTP 要求檢查器，以完全了解 HTTP 要求的大小、圖形和格式。您可以建立新的 RequestBin 並在邏輯應用程式 HTTP POST 動作中貼上 URL - 與您想要測試的任何內容 (運算式、另一個步驟輸出等等)。執行邏輯應用程式後，您可以重新整理 RequestBin，查看要求從邏輯應用程式引擎產生時是如何形成的。




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0601_2016-->