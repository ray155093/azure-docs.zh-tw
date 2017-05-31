---
title: "了解在您的邏輯應用程式中使用 Azure 服務匯流排連接器 | Microsoft Docs"
description: "使用 Azure App Service 建立邏輯應用程式。 連線到 Azure 服務匯流排來傳送及接收訊息。 您可以執行動作，例如傳送至佇列、傳送至主題、從佇列接收和從訂用帳戶接收。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e5d3301c90adf993b1cba35969dfe4dbeaeab499
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>開始使用 Azure 服務匯流排連接器
連線到 Azure 服務匯流排來傳送及接收訊息。 您可以執行動作，例如傳送至佇列、傳送至主題、從佇列接收和從訂用帳戶接收。

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)來開始。

## <a name="connect-to-service-bus"></a>連接到服務匯流排
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>使用服務匯流排觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>使用服務匯流排動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="view-the-swagger"></a>檢視 Swagger
請參閱 [Swagger 詳細資料](/connectors/servicebus/)。 

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。


