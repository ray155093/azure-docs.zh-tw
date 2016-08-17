<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="使用 Azure App Service 建立邏輯應用程式。Project Online 是專案組合管理 (PPM) 與 Microsoft 日常工作的彈性線上方案。Project Online 透過 Office 365 傳遞，可讓組織快速開始使用功能強大的專案管理功能，來規劃、設定優先順序以及管理專案和專案組合投資，不受場地和裝置的限制。"
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
ms.date="05/16/2016"
ms.author="deonhe"/>

# 開始使用 ProjectOnline 連接器

Project Online 是專案組合管理 (PPM) 與 Microsoft 日常工作的彈性線上方案。Project Online 透過 Office 365 傳遞，可讓組織快速開始使用功能強大的專案管理功能，來規劃、設定優先順序以及管理專案和專案組合投資，不受場地和裝置的限制。

您可從下列位置使用 ProjectOnline 連接器︰

- [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flow](http://flow.microsoft.com)

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

ProjectOnline 連接器可當成動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 ProjectOnline 連接器提供下列動作及/或觸發程序：

### ProjectOnline 動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|列出 Project Online 網站中的專案|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|在 Project Online 網站中建立新專案|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|在專案中建立新工作|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|在 Project Online 網站中建立企業資源|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|列出專案中已發佈的工作|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|簽出網站中的專案|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|簽入和發佈網站中現有的專案|
### ProjectOnline 觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|
|建立新專案時|每當建立新專案就會觸發流程|
|建立新資源時|建立新資源時就會觸發新流程|
|建立新工作時|建立新工作時就會觸發流程|


## 建立 ProjectOnline 的連線
若要使用 ProjectOnline 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 ProjectOnline 認證|

>[AZURE.INCLUDE [建立至 ProjectOnline 連線的步驟](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## ProjectOnline 的參考
適用的版本：1.0

## OnNewProject
建立新專案時：每當建立新專案就會觸發流程

```GET: /trigger/_api/ProjectData/Projects```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## OnNewResource
建立新資源時︰建立新資源時就會觸發新流程

```GET: /trigger/_api/ProjectData/Resources```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## OnNewTask
建立新工作時︰建立新工作時就會觸發流程

```GET: /trigger/_api/ProjectData/Tasks```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## ListProjects
列出專案：列出 Project Online 網站中的專案

```GET: /_api/ProjectServer/Projects```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateProject
建立新專案：在 Project Online 網站中建立新專案

```POST: /_api/ProjectServer/Projects```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|
|proj| |yes|body|無|要建立的新專案|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateTask
建立新工作：在專案中建立新工作

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|字串|yes|路徑|無|要加入工作中的專案唯一識別碼|
|工作| |yes|body|無|要加入專案中的新工作|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CreateResource
建立新資源：在 Project Online 網站中建立企業資源

```POST: /_api/ProjectServer/EnterpriseResources```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|
|resource| |yes|body|無|要加入專案中的新企業資源|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## ListTasks
列出工作：列出專案中已發佈的工作

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|字串|yes|路徑|無|擷取工作的專案唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## CheckoutProject
簽出專案：簽出網站中的專案

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|字串|yes|路徑|無|要加入工作中的專案唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## PublishProject
簽入和發佈專案：簽入和發佈網站中現有的專案

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|siteUrl|字串|yes|query|無|專案網站的根網站 URL (範例︰https://sampletenant.sharepoint.com/teams/sampleteam)|
|project\_id|字串|yes|路徑|無|簽入的專案唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## 物件定義 

### TriggerProjectsWrapper


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### TriggerProject


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|ProjectStartDate|字串|否 |
|ProjectFinishDate|字串|否 |
|ProjectCreatedDate|字串|否 |
|ProjectId|字串|否 |
|ProjectModifiedDate|字串|否 |
|ProjectType|integer|否 |
|ProjectName|字串|否 |



### TriggerResourcesWrapper


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### TriggerResource


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|ResourceId|字串|否 |
|ResourceBaseCalendar|字串|否 |
|ResourceBookingType|integer|否 |
|ResourceCanLevel|布林值|否 |
|ResourceCostPerUse|number|否 |
|ResourceCreatedDate|字串|否 |
|ResourceEarliestAvailableFrom|字串|否 |
|ResourceEmail|字串|否 |
|ResourceInitials|字串|否 |
|ResourceIsActive|布林值|否 |
|ResourceIsGeneric|布林值|否 |
|ResourceLatestAvailableTo|字串|否 |
|ResourceModifiedDate|字串|否 |
|ResourceName|字串|否 |
|ResourceStatsuName|字串|否 |
|ResourceType|integer|否 |
|TypeDescription|字串|否 |
|TypeName|字串|否 |



### TriggerTasksWrapper


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### TriggerTask


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|ProjectId|字串|否 |
|TaskId|字串|否 |
|ProjectName|字串|否 |
|TaskName|字串|否 |
|TaskCreatedDate|字串|否 |
|TaskModifieddate|字串|否 |
|TaskStartDate|字串|否 |
|TaskFinishDate|字串|否 |
|TaskPriority|integer|否 |
|TaskIsActive|布林值|否 |



### NewProject


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|是 |
|說明|字串|否 |
|啟動|字串|否 |



### NewReource


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|是 |
|IsBudget|布林值|否 |
|IsGeneric|布林值|否 |
|IsInactive|布林值|否 |



### 隨附此逐步解說的專案


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|ApprovedStart|字串|否 |
|ApprovedEnd|字串|否 |
|CheckedOutDate|字串|否 |
|CheckOutDescription|字串|否 |
|CheckOutId|字串|否 |
|CreatedDate|字串|否 |
|識別碼|字串|否 |
|IsCheckedOut|布林值|否 |
|LastPublishedDate|字串|否 |
|LastSavedDate|字串|否 |
|OptimizerDecision|integer|否 |
|PlannerDecision|integer|否 |
|ProjectType|integer|否 |
|名稱|字串|否 |
|WinprojVersion|字串|否 |



### ProjectsWrapper


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### NewTask


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|參數|沒有定義|是 |



### TaskParameters


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|名稱|字串|是 |
|注意事項|字串|否 |
|啟動|字串|否 |
|持續時間|字串|否 |



### EnterpriseResource


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|CanLevel|布林值|否 |
|代碼|字串|否 |
|CostAccrual|integer|否 |
|CostCenter|字串|否 |
|建立時間|字串|否 |
|DefaultBookingType|integer|否 |
|電子郵件|字串|否 |
|ExternalId|字串|否 |
|群組|字串|否 |
|HireDate|字串|否 |
|識別碼|字串|否 |
|Initials|字串|否 |
|IsActive|布林值|否 |
|IsBudget|布林值|否 |
|IsCheckedOut|布林值|否 |
|IsGeneric|布林值|否 |
|IsTeam|布林值|否 |
|MaterialLabel|字串|否 |
|修改時間|字串|否 |
|名稱|字串|否 |
|Phonetics|字串|否 |
|ResourceType|integer|否 |
|TerminationDate|字串|否 |



### TasksWrapper


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|value|array|否 |



### 工作


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|建立時間|字串|否 |
|修改時間|字串|否 |
|啟動|字串|否 |
|完成|字串|否 |
|名稱|字串|否 |
|識別碼|字串|否 |
|優先順序|integer|否 |
|PercentComplete|integer|否 |
|注意事項|字串|否 |
|連絡人|字串|否 |


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->