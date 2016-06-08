<properties
pageTitle="在您的 Logic Apps 中使用 Azure 服務匯流排連接器 | Microsoft Azure"
description="開始在您的 Microsoft Azure App Service Logic Apps 中使用 Azure 服務匯流排連接器"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/23/2016"
ms.author="deonhe"/>

# 開始使用 Azure 服務匯流排連接器 

連線到 Azure 服務匯流排來傳送及接收訊息。您可以執行動作，例如傳送至佇列、傳送至主題、從佇列接收、從訂用帳戶接收等等。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以利用 Azure 服務匯流排來：

* 建置邏輯應用程式  

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 我們來談談觸發程序及動作。

Azure 服務匯流排連接器可當做動作來使用；它擁有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 Azure 服務匯流排連接器提供下列動作及/或觸發程序：

### Azure 服務匯流排的動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|SendMessage|將訊息傳送至 Azure 服務匯流排的佇列或主題。|
### Azure 服務匯流排的觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|
|GetMessageFromQueue|從 Azure 服務匯流排的佇列取得新訊息。|
|GetMessageFromTopic|從 Azure 服務匯流排的主題訂用帳戶取得新訊息。|


## 建立至 Azure 服務匯流排的連線
如要使用 Azure 服務匯流排連接器，您必須先建立**連線**，然後提供下列屬性的詳細資料：

>[AZURE.INCLUDE [建立至 ServiceBus 連線的步驟](../../includes/connectors-create-api-servicebus.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## Azure 服務匯流排 REST API 參考
#### 本文件適用的版本：1.0


### 將訊息傳送至 Azure 服務匯流排的佇列或主題。
**```POST: /{entityName}/messages```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|訊息| |yes|body|無|服務匯流排訊息|
|entityName|字串|yes|路徑|無|佇列或主題的名稱|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 從 Azure 服務匯流排的佇列取得新訊息。
**```GET: /{queueName}/messages/head```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|queueName|字串|yes|路徑|無|佇列的名稱。|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 從 Azure 服務匯流排的主題訂用帳戶取得新訊息。
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|topicName|字串|yes|路徑|無|主題的名稱。|
|subscriptionName|字串|yes|路徑|無|主題訂用帳戶的名稱。|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



## 物件定義： 

 **ServiceBusMessage**：訊息包含內容及屬性

ServiceBusMessage 的必要屬性：

ContentTransferEncoding

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|ContentData|字串|
|ContentType|字串|
|ContentTransferEncoding|字串|
|屬性|物件|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0525_2016-->