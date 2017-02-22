---
title: "Logic Apps 範例和案例 | Microsoft Docs"
description: "請參閱常見的邏輯應用程式範例，並了解如何實作常見的案例"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: ebdac3845e3b635ea6be7de41df9b389915e5d39


---
# <a name="logic-apps-examples-and-common-scenarios"></a>Logic Apps 範例和常見案例
本文件詳述常見案例和範例，協助您了解可使用邏輯應用程式將商務程序自動化的一些方法。 

## <a name="custom-triggers-and-actions"></a>自訂觸發程序和動作
有幾種方法可從另一個應用程式觸發邏輯應用程式。 以下是一些常見範例︰

* [建立自訂觸發程序或動作](../logic-apps/logic-apps-create-api-app.md)
* [長時間執行的動作](../logic-apps/logic-apps-create-api-app.md)
* [HTTP 要求觸發程序 (POST)](logic-apps-http-endpoint.md)
* [Webhook 觸發程序和動作](../logic-apps/logic-apps-create-api-app.md)
* [輪詢觸發程序](../logic-apps/logic-apps-create-api-app.md)

### <a name="scenarios"></a>案例
* [要求同步回應](logic-apps-http-endpoint.md)
* [使用 SMS 的要求回應](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>錯誤處理和記錄
* [例外狀況和錯誤處理](logic-apps-exception-handling.md)
* [設定 Azure 警示和診斷](logic-apps-monitor-your-logic-apps.md)

### <a name="scenarios"></a>案例
* [使用案例︰錯誤和例外狀況處理](logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>部署和管理
* [建立自動部署](../logic-apps/logic-apps-create-deploy-template.md)
* [在 Visual Studio 中建置和部署 Logic Apps](logic-apps-deploy-from-vs.md)
* [監視邏輯應用程式](logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>內容類型、轉換 (Conversion) 及轉換 (Transformation)
Logic Apps [工作流程定義語言](http://aka.ms/logicappsdocs) 包含許多功能可讓您進行轉換，以及使用不同的內容類型。 此外，當資料流經工作流程時，引擎將盡其所能地保留內容類型。

* [處理內容類型](../logic-apps/logic-apps-content-type.md)，例如 application/json、application/xml 和 text/plain
* [撰寫工作流程定義](../logic-apps/logic-apps-author-definitions.md)
* [工作流程定義語言參考](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>批次和迴圈
* [SplitOn](logic-apps-loops-and-scopes.md)
* [ForEach](logic-apps-loops-and-scopes.md)
* [Until](logic-apps-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>與 Azure Functions 整合
* [Azure Functions 整合](../logic-apps/logic-apps-azure-functions.md)

### <a name="scenarios"></a>案例
* [Azure Function 做為服務匯流排觸發程序](logic-apps-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP、REST 和 SOAP
* [呼叫 SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

我們將持續在本文件中加入範例和案例。 請使用下列意見段落，讓我們知道您想要在這裡看到的範例或案例。




<!--HONumber=Jan17_HO3-->


