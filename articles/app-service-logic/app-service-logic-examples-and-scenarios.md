<properties
   pageTitle="邏輯應用程式範例和案例 | Microsoft Azure"
   description="請參閱常見的邏輯應用程式範例，並了解如何實作常見的案例"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/25/2016"
   ms.author="deonhe"/>

# 邏輯應用程式範例和常見案例

本文件詳述常見案例和範例，協助您了解可使用邏輯應用程式將商務程序自動化的一些方法。

## 觸發邏輯應用程式

有幾種方法可從另一個應用程式觸發邏輯應用程式。以下是其中幾個方法：

- [HTTP 要求 (POST)](app-service-logic-http-endpoint.md)
- [接收 webhook](app-service-logic-create-api-app.md)
- [輪詢端點](app-service-logic-create-api-app.md)

### 案例

- [邏輯應用程式要求同步回應](app-service-logic-http-endpoint.md)

## 內容類型、轉換 (Conversion) 及轉換 (Transformation)

Logic Apps [工作流程定義語言](http://aka.ms/logicappsdocs)包含許多功能可讓您進行轉換，以及使用不同的內容類型。此外，當資料流經工作流程時，引擎將盡其所能地保留內容類型。

- [處理內容類型](app-service-logic-content-type.md)，例如 application/json、application/xml 和 plain/text
- [工作流程定義語言](http://aka.ms/logicappsdocs)

## 長時間執行的動作

- [建立長時間執行的動作](app-service-logic-create-api-app.md)

## 批次和迴圈

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## 與巢狀工作流程和 Azure Functions 整合

- [Azure Functions 整合](app-service-logic-azure-functions.md)

## HTTP、REST 和 SOAP

 - [呼叫 SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


我們將持續在本文件中加入範例和案例。請使用下列意見段落，讓我們知道您想要在這裡看到的範例或案例。

<!---HONumber=AcomDC_0525_2016-->