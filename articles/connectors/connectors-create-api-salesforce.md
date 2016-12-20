---
title: "了解在邏輯應用程式中使用 Salesforce 連接器 | Microsoft Docs"
description: "使用 Azure App Service 建立邏輯應用程式。 Salesforce 連接器提供搭配 Salesforce 物件使用的 API。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f2f9a4d182527d2772e78d78a0c10bd94742d0fe


---
# <a name="get-started-with-the-salesforce-connector"></a>開始使用 Salesforce 連接器
Salesforce 連接器提供搭配 Salesforce 物件使用的 API。

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)來開始。

## <a name="connect-to-salesforce-connector"></a>連接至 Salesforce 連接器
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。  

### <a name="create-a-connection-to-salesforce-connector"></a>建立至 Salesforce 連接器的連線
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>使用 Salesforce 連接器觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>新增條件
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>使用 Salesforce 連接器動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="technical-details"></a>技術詳細資訊
以下是有關這個連接支援的觸發程序、動作和回應的詳細資料︰

## <a name="salesforce-connector-triggers"></a>Salesforce 連接器觸發程序
Salesforce 連接器具有下列觸發程序︰  

| 觸發程序 | 說明 |
| --- | --- |
| [當物件建立時](connectors-create-api-salesforce.md#when-an-object-is-created) |當有物件建立時，此作業就會觸發流程。 |
| [當物件遭到修改時](connectors-create-api-salesforce.md#when-an-object-is-modified) |當有物件遭到修改時，此作業就會觸發流程。 |

## <a name="salesforce-connector-actions"></a>Salesforce 連接器動作
Salesforce 連接器具有下列動作︰

| 動作 | 說明 |
| --- | --- |
| [取得物件](connectors-create-api-salesforce.md#get-objects) |這項作業會取得特定物件類型 (如「潛在客戶」) 的物件。 |
| [建立物件](connectors-create-api-salesforce.md#create-object) |這項作業會建立物件。 |
| [取得物件](connectors-create-api-salesforce.md#get-object) |這項作業會取得物件。 |
| [刪除物件](connectors-create-api-salesforce.md#delete-object) |這項作業會刪除物件。 |
| [更新物件](connectors-create-api-salesforce.md#update-object) |這項作業會更新物件。 |
| [取得物件類型](connectors-create-api-salesforce.md#get-object-types) |這項作業會列出可用的物件類型。 |

### <a name="action-details"></a>動作詳細資料
以下是此連接器動作和觸發程序以及其回應的詳細資料︰

### <a name="get-objects"></a>取得物件
這項作業會取得特定物件類型 (如「潛在客戶」) 的物件。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料表 * |物件類型 |Salesforce 物件類型 (如「潛在客戶」) |
| $filter |篩選查詢 |用來限制項目數目的 ODATA filter 查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要擷取的項目數目上限 (預設值 = 256) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

### <a name="create-object"></a>建立物件
這項作業會建立物件。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料表 * |物件類型 |物件類型 (如「潛在客戶」) |
| 項目 * |Object |要建立的物件 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
項目

| 屬性名稱 | 資料類型 |
| --- | --- |
| ItemInternalId |字串 |

### <a name="get-object"></a>取得物件
這項作業會取得物件。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料表 * |物件類型 |Salesforce 物件類型 (如「潛在客戶」) |
| 識別碼* |物件識別碼 |要取得的物件識別碼 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
項目

| 屬性名稱 | 資料類型 |
| --- | --- |
| ItemInternalId |字串 |

### <a name="delete-object"></a>刪除物件
這項作業會刪除物件。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料表 * |物件類型 |物件類型 (如「潛在客戶」) |
| 識別碼* |物件識別碼 |要刪除的物件識別碼 |

* 表示這是必要屬性

### <a name="update-object"></a>更新物件
這項作業會更新物件。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料表 * |物件類型 |物件類型 (如「潛在客戶」) |
| 識別碼* |物件識別碼 |要更新的物件識別碼 |
| 項目 * |Object |屬性已更新的物件 |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
項目

| 屬性名稱 | 資料類型 |
| --- | --- |
| ItemInternalId |字串 |

### <a name="when-an-object-is-created"></a>當物件建立時
當有物件建立時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料表 * |物件類型 |物件類型 (如「潛在客戶」) |
| $filter |篩選查詢 |用來限制項目數目的 ODATA filter 查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要擷取的項目數目上限 (預設值 = 256) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

### <a name="when-an-object-is-modified"></a>當物件遭到修改時
當有物件遭到修改時，此作業就會觸發流程。 

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料表 * |物件類型 |物件類型 (如「潛在客戶」) |
| $filter |篩選查詢 |用來限制項目數目的 ODATA filter 查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要擷取的項目數目上限 (預設值 = 256) |

* 表示這是必要屬性

#### <a name="output-details"></a>輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

### <a name="get-object-types"></a>取得物件類型
這項作業會列出可用的物件類型。 

這個呼叫沒有參數

#### <a name="output-details"></a>輸出詳細資料
TablesList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

## <a name="http-responses"></a>HTTP 回應
上述動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰ 

| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤。 |
| 預設值 |作業失敗。 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


