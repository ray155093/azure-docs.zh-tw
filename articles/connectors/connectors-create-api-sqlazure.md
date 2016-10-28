<properties
    pageTitle="在您的 Logic Apps 中新增 Azure SQL Database 連接器 | Microsoft Azure"
    description="搭配 REST API 參數來使用 Azure SQL Database 連接器的概觀"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/25/2016"
   ms.author="mandia"/>


# 開始使用 Azure SQL Database 連接器
使用 Azure SQL Database 連接器，為組織建立工作流程來管理資料表中的資料。

利用 SQL Database，您可以：

- 藉由在客戶資料庫中新增新客戶或在訂單資料庫中更新訂單，以建置工作流程。
- 使用動作來取得一列資料、插入新的資料列，甚至加以刪除。例如，當有記錄在 Dynamics CRM Online 中建立時 (觸發程序)，則在 Azure SQL Database 插入資料列 (動作)。

本主題說明如何在邏輯應用程式中使用 SQL Database 連接器，並且也列出動作。

>[AZURE.NOTE] 這個版本的文章適用於 Logic Apps 公開上市版本 (GA)。

若要深入瞭解 Logic Apps，請參閱[什麼是邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)以及[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 連線到 Azure SQL Database

您必須先建立與服務的「連線」，才能透過邏輯應用程式存取任何服務。連線可讓邏輯應用程式與另一個服務連線。例如，若要連線到 SQL Database，您必須先建立 SQL Database「連線」。若要建立連線，您需要輸入平常用來存取所連線服務的認證。因此，在 SQL Database 中，請輸入 SQL Database 認證來建立連線。

#### 建立連線

>[AZURE.INCLUDE [建立至 SQL Azure 的連線](../../includes/connectors-create-api-sqlazure.md)]

## 使用觸發程序

此連接器並沒有任何觸發程序。請使用其他觸發程序來啟動邏輯應用程式，例如循環觸發程序、HTTP Webhook 觸發程序、其他連接器適用的觸發程序等等。[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)可提供範例。

## 使用動作
	
動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 選取加號。您會看到幾個選擇︰[新增動作]、[新增條件] 或其中一個 [其他] 選項。

	![](./media/connectors-create-api-sqlazure/add-action.png)

2. 選擇 [新增動作]。

3. 在文字方塊中，輸入「sql」以取得所有可用動作的清單。

	![](./media/connectors-create-api-sqlazure/sql-1.png)

4. 在我們的範例中，選擇 [SQL Server - 取得資料列]。如果連線已存在，則選取下拉式清單中的 [資料表名稱]，然後輸入想要傳回的 [資料列識別碼]。

	![](./media/connectors-create-api-sqlazure/sample-table.png)

	如果系統提示您輸入連線資訊，請輸入詳細資料以建立連線。本主題的[建立連線](connectors-create-api-sqlazure.md#create-the-connection)一節會說明這些屬性。

	> [AZURE.NOTE] 在此範例中，我們會從資料表傳回資料列。若要查看此資料列中的資料，請新增另一個動作，以使用資料表中的欄位建立檔案。例如，新增 OneDrive 動作，使用 [FirstName] 和 [LastName] 欄位在雲端儲存體帳戶中建立新檔案。

5. **儲存**您的變更 (工具列的左上角)。邏輯應用程式將會儲存，而且可能會自動啟用。


## 技術詳細資料

## SQL Database 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。SQL Database 連接器包含下列動作。

|動作|說明|
|--- | ---|
|[ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure)|在 SQL 中執行預存程序|
|[GetRow](connectors-create-api-sqlazure.md#get-row)|擷取 SQL 資料表中的單一資料列|
|[GetRows](connectors-create-api-sqlazure.md#get-rows)|從 SQL 資料表擷取多個資料列|
|[InsertRow](connectors-create-api-sqlazure.md#insert-row)|在 SQL 資料表中插入新的資料列|
|[DeleteRow](connectors-create-api-sqlazure.md#delete-row)|刪除 SQL 資料表中的資料列|
|[GetTables](connectors-create-api-sqlazure.md#get-tables)|擷取 SQL 資料庫中的資料表|
|[UpdateRow](connectors-create-api-sqlazure.md#update-row)|更新 SQL 資料表中現有的資料列|

### 動作詳細資料

在本節中，請查看每個動作的特定詳細資料，包括任何必要或選擇性的輸入屬性，以及任何與連接器相關聯的對應輸出。


#### 執行預存程序
在 SQL 中執行預存程序。

| 屬性名稱| 顯示名稱 |說明|
| ---|---|---|
|程序 * | 程序名稱 | 您要執行的預存程序名稱 |
|參數 * | 輸入參數 | 參數是動態的，而且以您選擇的預存程序為基礎。<br/><br/> 例如，如果要使用 Adventure Works 範例資料庫，請選擇「ufnGetCustomerInformation」預存程序。系統會顯示**客戶識別碼**輸入參數。請輸入「6」或其他客戶識別碼的其中一個。 |

星號 (*) 代表必要屬性。

##### 輸出詳細資料
ProcedureResult︰帶有預存程序執行的結果

| 屬性名稱 | 資料類型 | 說明 |
|---|---|---|
|OutputParameters|物件|輸出參數值 |
|ReturnCode|integer|傳回程序的代碼 |
|ResultSets|物件| 結果集|


#### 取得單一資料列 
從 SQL 資料表擷取單一資料列。

| 屬性名稱| 顯示名稱 |說明|
| ---|---|---|
|資料表 * | 資料表名稱 |SQL 資料表名稱|
|識別碼 * | 資料列識別碼 |要擷取之資料列的唯一識別碼|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
項目

| 屬性名稱 | 資料類型 |
|---|---|
|ItemInternalId|字串|


#### 取得多個資料列 
從 SQL 資料表擷取多個資料列。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料表名稱|SQL 資料表名稱|
|$skip|略過計數|要略過的項目數目 (預設值 = 0)|
|$top|最大取得計數|要擷取的項目數目上限 (預設值 = 256)|
|$filter|篩選查詢|用來限制項目數目的 ODATA filter 查詢|
|$orderby|排序依據|用來指定項目順序的 ODATA orderBy 查詢|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
|---|---|
|value|array|


#### 插入資料列 
在 SQL 資料表中插入新的資料列。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料表名稱|SQL 資料表名稱|
|項目 *|資料列|要在指定的 SQL 資料表中插入的資料列|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
項目

| 屬性名稱 | 資料類型 |
|---|---|
|ItemInternalId|字串|


#### 刪除資料列 
刪除 SQL 資料表中的資料列。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料表名稱|SQL 資料表名稱|
|識別碼*|資料列識別碼|要刪除的資料列的唯一識別碼|

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。

#### 取得資料表 
擷取 SQL 資料庫中的資料表。

這個呼叫沒有參數。

##### 輸出詳細資料 
TablesList

| 屬性名稱 | 資料類型 |
|---|---|
|value|array|

#### 更新資料列 
更新 SQL 資料表中現有的資料列。

|屬性名稱| 顯示名稱|說明|
| ---|---|---|
|資料表 *|資料表名稱|SQL 資料表名稱|
|識別碼*|資料列識別碼|要更新的資料列的唯一識別碼|
|項目 *|資料列|值已更新的資料列|

星號 (*) 代表必要屬性。

##### 輸出詳細資料  
項目

| 屬性名稱 | 資料類型 |
|---|---|
|ItemInternalId|字串|


### HTTP 回應

呼叫不同動作時，您可能會收到特定回應。下表概述回應及其說明︰

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


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。請到我們的 [API 清單](apis-list.md)探索 Logic Apps 中其他可用的連接器。

<!---HONumber=AcomDC_0727_2016-->