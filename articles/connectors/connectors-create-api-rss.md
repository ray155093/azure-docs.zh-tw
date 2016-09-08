<properties
pageTitle="RSS | Microsoft Azure"
description="使用 Azure App Service 建立邏輯應用程式。RSS 連接器可讓使用者發佈和擷取摘要項目。它也可讓使用者在新項目發佈到摘要中時觸發作業。"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# 開始使用 RSS 連接器
RSS 是用來發佈經常更新之內容 (像是部落格文章和新聞標題) 的熱門 Web 同步發佈格式。許多內容發佈者都提供可讓使用者訂閱的 RSS 摘要。請使用 RSS 連接器以在 RSS 摘要中發佈了新項目時擷取摘要資訊和觸發流程。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

RSS 連接器可當做動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 RSS 連接器提供下列動作及/或觸發程序：

### RSS 動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|取得所有 RSS 摘要項目。|
### RSS 觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|
|發佈新的摘要項目時|發佈新摘要時就會觸發工作流程|


## 建立 RSS 的連線

>[AZURE.INCLUDE [建立至 RSS 摘要連線的步驟](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## RSS 的參考
適用的版本：1.0

## OnNewFeed
發佈新的摘要項目時︰發佈新的摘要時就會觸發工作流程

```GET: /OnNewFeed```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|feedUrl|string|yes|query|無|摘要 URL|

#### Response

|Name|說明|
|---|---|
|200|OK|
|202|已接受|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## ListFeedItems
列出所有的 RSS 摘要項目：取得所有 RSS 摘要項目。

```GET: /ListFeedItems```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|feedUrl|string|yes|query|無|摘要 URL|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|202|已接受|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## 物件定義 

### TriggerBatchResponse[FeedItem]


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### FeedItem


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|id|string|是 |
|title|string|是 |
|內容|string|是 |
|連結|array|否 |
|updatedOn|string|否 |


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->