---
title: 將 Dynamics CRM Online 連接器新增到 Logic Apps | Microsoft Docs
description: 使用 Azure App Service 建立邏輯應用程式。Dynamics CRM Online 連接提供者提供 API，以便使用 Dynamics CRM Online 上的實體。
services: logic-apps
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/15/2016
ms.author: mandia

---
# 開始使用 Dynamics CRM Online 連接器
連線到 Dynamics CRM Online 來建立新的記錄、更新項目等等。您可以利用 CRM Online 來：

* 根據您從 CRM Online 所取得的資料，來建置您的商務流程。
* 使用會刪除檔案、取得實體等等的動作。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當 CRM 中有項目更新時，您可以利用 Office 365 來傳送電子郵件。

本主題說明如何在邏輯應用程式中使用 Dynamics CRM Online 連接器，並且也列出觸發程序和動作。

> [!NOTE]
> 這個版本的文章適用於 Logic Apps 公開上市版本 (GA)。
> 
> 

若要深入瞭解 Logic Apps，請參閱[什麼是邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)以及[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 連接至 Dynamics CRM Online
您必須先建立與服務的「連線」，才能透過邏輯應用程式存取任何服務。連線可讓邏輯應用程式與另一個服務連線。例如，若要連線到 Dynamics，您必須先有 Dynamics CRM Online「連線」。若要建立連線，請輸入平常用來存取所要連線之服務的認證。因此，在 Dynamics 中，請輸入 Dynamics CRM Online 帳戶的認證以建立連線。

### 建立連線
> [!INCLUDE [建立 Dynamics CRM Online 連接提供者之連線的步驟](../../includes/connectors-create-api-crmonline.md)]
> 
> 

## 使用觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。觸發程序會以您想要的間隔和頻率「輪詢」服務。[深入了解觸發程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 在邏輯應用程式中，輸入 "dynamics" 以取得觸發程序的清單︰
   
    ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. 選取 [Dynamics CRM Online - 建立記錄時]。如果連線已存在，則選取下拉式清單中的組織和實體。
   
    ![](./media/connectors-create-api-crmonline/select-organization.png)
   
    如果系統提示您登入，則輸入登入詳細資料來建立連線。本主題中的[建立連線](connectors-create-api-crmonline.md#create-the-connection)一節會列出步驟。
   
   > [!NOTE]
   > 在此範例中，邏輯應用程式會在建立記錄時執行。若要查看此觸發程序的結果，請新增另一個動作，以傳送電子郵件給您。例如，新增 Office 365「傳送電子郵件」動作，以在加入新記錄時傳送電子郵件給您。
   > 
   > 
3. 選取 [編輯] 按鈕，然後設定 [頻率] 和 [間隔] 值。例如，如果您希望觸發程序每隔 15 分鐘輪詢一次，則將 [頻率] 設定為 [分鐘] 並將 [**間隔]** 設定為 [15]。
   
    ![](./media/connectors-create-api-crmonline/edit-properties.png)
4. **儲存**您的變更 (工具列的左上角)。邏輯應用程式將會儲存，而且可能會自動啟用。

## 使用動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

1. 選取加號。您會看到幾個選擇︰[新增動作]、[新增條件] 或其中一個 [其他] 選項。
   
    ![](./media/connectors-create-api-crmonline/add-action.png)
2. 選擇 [新增動作]。
3. 在文字方塊中，輸入 “dynamics” 以取得所有可用動作的清單。
   
    ![](./media/connectors-create-api-crmonline/dynamics-actions.png)
4. 在本例中，選擇 [Dynamics CRM Online - 更新記錄]。如果連線已存在，則選擇 [組織名稱]、[實體名字] 和其他屬性︰
   
    ![](./media/connectors-create-api-crmonline/sample-action.png)
   
    如果系統提示您輸入連線資訊，請輸入詳細資料以建立連線。本主題的[建立連線](connectors-create-api-crmonline.md#create-the-connection)一節會說明這些屬性。
   
   > [!NOTE]
   > 在此範例中，我們會更新 CRM Online 中的現有記錄。您可以使用另一個觸發程序的輸出來更新記錄。例如，新增 SharePoint「當現有項目遭到修改時」觸發程序。然後新增 CRM Online「更新資料錄」動作，以使用 SharePoint 欄位來更新 CRM Online 中的現有記錄。
   > 
   > 
5. **儲存**您的變更 (工具列的左上角)。邏輯應用程式將會儲存，而且可能會自動啟用。

## 技術詳細資料
## 觸發程序
| 觸發程序 | 說明 |
| --- | --- |
| [當檔案建立時](connectors-create-api-crmonline.md#when-a-record-is-created) |當 CRM 中有物件建立時，就會觸發某個流程。 |
| [當記錄更新時](connectors-create-api-crmonline.md#when-a-record-is-updated) |當 CRM 中有物件遭到修改時，就會觸發某個流程。 |
| [當記錄刪除時](connectors-create-api-crmonline.md#when-a-record-is-deleted) |當 CRM 中有物件刪除時，就會觸發某個流程。 |

## 動作
| 動作 | 說明 |
| --- | --- |
| [列出記錄](connectors-create-api-crmonline.md#list-records) |這項作業會取得實體的記錄。 |
| [建立新的記錄](connectors-create-api-crmonline.md#create-a-new-record) |這項作業會建立實體的新記錄。 |
| [取得記錄](connectors-create-api-crmonline.md#get-record) |這項作業會取得實體的指定記錄。 |
| [刪除記錄](connectors-create-api-crmonline.md#delete-a-record) |這項作業會從實體集合中刪除記錄。 |
| [更新記錄](connectors-create-api-crmonline.md#update-a-record) |這項作業會更新實體的現有記錄。 |

### 觸發程序和動作詳細資料
在本節中，請查看每個觸發程序和動作的特定詳細資料，包括任何必要或選擇性的輸入屬性，以及任何與連接器相關聯的對應輸出。

#### 當檔案建立時
當 CRM 中有物件建立時，就會觸發某個流程。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |CRM 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要取得的項目數目上限 (預設值 = 256) |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |

星號 (*) 代表必要屬性。

##### 輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

#### 當記錄更新時
當 CRM 中有物件遭到修改時，就會觸發某個流程。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |CRM 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要取得的項目數目上限 (預設值 = 256) |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |

星號 (*) 代表必要屬性。

##### 輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

#### 當記錄刪除時
當 CRM 中有物件刪除時，就會觸發某個流程。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |CRM 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要取得的項目數目上限 (預設值 = 256) |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |

星號 (*) 代表必要屬性。

##### 輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

#### 列出記錄
這項作業會取得實體的記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |CRM 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| $skip |略過計數 |要略過的項目數目 (預設值 = 0) |
| $top |最大取得計數 |要取得的項目數目上限 (預設值 = 256) |
| $filter |篩選查詢 |用來限制傳回項目的 ODATA 篩選查詢 |
| $orderby |排序依據 |用來指定項目順序的 ODATA orderBy 查詢 |

星號 (*) 代表必要屬性。

##### 輸出詳細資料
ItemsList

| 屬性名稱 | 資料類型 |
| --- | --- |
| value |array |

#### 建立新的記錄
這項作業會建立實體的新記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |CRM 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。

#### 取得記錄
這項作業會取得實體的指定記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |CRM 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| 識別碼* |項目識別碼 |指定記錄的識別碼 |

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。

#### 刪除記錄
這項作業會從實體集合中刪除記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |CRM 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| 識別碼* |項目識別碼 |指定記錄的識別碼 |

星號 (*) 代表必要屬性。

#### 更新記錄
這項作業會更新實體的現有記錄。

| 屬性名稱 | 顯示名稱 | 說明 |
| --- | --- | --- |
| 資料集* |組織名稱 |CRM 組織的名稱 (例如 Contoso) |
| 資料表 * |實體名稱 |實體的名稱 |
| 識別碼* |記錄識別碼 |指定記錄的識別碼 |

星號 (*) 代表必要屬性。

##### 輸出詳細資料
無。

## HTTP 回應
動作和觸發程序可以傳回一或多個下列的 HTTP 狀態碼︰

| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 202 |已接受 |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。發生未知錯誤。 |
| 預設值 |作業失敗。 |

## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。請到我們的 [API 清單](apis-list.md)探索 Logic Apps 中其他可用的連接器。

<!---HONumber=AcomDC_0817_2016-->