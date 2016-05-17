<properties
pageTitle="SQL 連接器 | Microsoft Azure"
description="使用 Azure App Service 建立邏輯應用程式。SQL 連接器提供搭配 SQL 資料庫使用的 API。"
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# 開始使用 SQL 連接器的連接器



您可以從下列位置使用 SQL 連接器的連接器︰

- [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flow](http://flows.microsoft.com)  

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

SQL 連接器的連接器可當成動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 SQL 連接器的連接器提供下列動作及/或觸發程序：

### SQL 連接器動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|[GetTables](connectors-create-api-sqlconnector.md#gettables)|擷取 SQL 資料庫中的資料表|
|[PostItem](connectors-create-api-sqlconnector.md#postitem)|在 SQL 資料表中插入新的資料列|
|[GetItem](connectors-create-api-sqlconnector.md#getitem)|擷取 SQL 資料表中的單一資料列|
|[DeleteItem](connectors-create-api-sqlconnector.md#deleteitem)|刪除 SQL 資料表中的資料列|
|[PatchItem](connectors-create-api-sqlconnector.md#patchitem)|更新 SQL 資料表中現有的資料列|
### SQL 連接器觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|


## 建立 SQL 連接器的連線
若要使用 SQL 連接器建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

|屬性| 必要|說明|
| ---|---|---|
|SqlConnectionString|是|提供您的 SQL 連接字串|
建立連接後，您就可以用它執行動作，並接聽本文所述的觸發程序。

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## SQL 連接器的參考
適用的版本：1.0

## GetTables
取得資料表：擷取 SQL 資料庫中的資料表

```GET: /datasets/default/tables```

這個呼叫沒有參數
#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## PostItem
插入資料列：在 SQL 資料表中插入新的資料列

```POST: /datasets/default/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|item| |yes|body|無|要在指定的 SQL 資料表中插入的資料列|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## GetItem
取得資料列：擷取 SQL 資料表中的單一資料列

```GET: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|id|字串|yes|路徑|無|要擷取之資料列的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## DeleteItem
刪除資料列：刪除 SQL 資料表中的資料列

```DELETE: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|id|字串|yes|路徑|無|要刪除的資料列的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## PatchItem
更新資料列：更新 SQL 資料表中現有的資料列

```PATCH: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|SQL 資料表名稱|
|id|字串|yes|路徑|無|要更新的資料列的唯一識別碼|
|item| |yes|body|無|值已更新的資料列|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## 物件定義 

### DataSetsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|表格式|沒有定義|否 |
|blob|沒有定義|否 |



### TabularDataSetsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|來源|字串|否 |
|displayName|字串|否 |
|urlEncoding|字串|否 |
|tableDisplayName|字串|否 |
|tablePluralName|字串|否 |



### BlobDataSetsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|來源|字串|否 |
|displayName|字串|否 |
|urlEncoding|字串|否 |



### TableMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|否 |
|title|字串|否 |
|x-ms-permission|字串|否 |
|x-ms-capabilities|沒有定義|否 |
|結構描述|沒有定義|否 |



### TableCapabilitiesMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|sortRestrictions|沒有定義|否 |
|filterRestrictions|沒有定義|否 |
|filterFunctions|array|否 |



### Object


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|



### TableSortRestrictionsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|sortable|布林值|否 |
|unsortableProperties|array|否 |
|ascendingOnlyProperties|array|否 |



### TableFilterRestrictionsMetadata


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|filterable|布林值|否 |
|nonFilterableProperties|array|否 |
|requiredProperties|array|否 |



### DataSetsList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### DataSet


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|否 |
|DisplayName|字串|否 |



### 資料表


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|否 |
|DisplayName|字串|否 |



### 項目


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|ItemInternalId|字串|否 |



### TablesList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### ItemsList


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->