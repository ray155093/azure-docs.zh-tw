<properties
pageTitle="GitHub | Microsoft Azure"
description="使用 Azure App Service 建立邏輯應用程式。GitHub 是 Web 架構的 Git 儲存機制裝載服務。它提供所有 Git 分散式修訂控制項和來源程式碼管理 (SCM) 功能，也加入自己的功能。"
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# 開始使用 GitHub 連接器



您可從下列位置使用 GitHub 連接器︰

- [邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flow](http://flows.microsoft.com)  

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。

您可以從立即建立邏輯應用程式開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

GitHub 連接器可當成動作使用，它有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 GitHub 連接器提供下列動作及/或觸發程序：

### GitHub 動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|建立問題|
### GitHub 觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|
|開啟問題時|開啟問題|
|關閉問題時|關閉問題|
|指派問題時|指派問題|


## 建立 GitHub 的連線
若要使用 GitHub 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 GitHub 認證|
建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。

>[AZURE.INCLUDE [建立至 GitHub 連線的步驟](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## GitHub 的參考
適用的版本：1.0

## CreateIssue
建立問題︰建立問題

```POST: /repos/{repositoryOwner}/{repositoryName}/issues```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|repositoryOwner|字串|yes|路徑|無|儲存機制擁有者|
|repositoryName|字串|yes|路徑|無|儲存機制名稱|
|issueBasicDetails| |yes|body|無|問題詳細資料|

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


## IssueOpened
開啟問題時︰開啟問題

```GET: /trigger/issueOpened```

這個呼叫沒有參數
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


## IssueClosed
關閉問題時︰關閉問題

```GET: /trigger/issueClosed```

這個呼叫沒有參數
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


## IssueAssigned
指派問題時︰指派問題

```GET: /trigger/issueAssigned```

這個呼叫沒有參數
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

### IssueBasicDetailsModel


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|title|字串|是 |
|body|字串|是 |
|受託人|字串|是 |



### IssueDetailsModel


| 屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|title|字串|是 |
|body|字串|是 |
|受託人|字串|是 |
|number|字串|否 |
|state|字串|否 |
|created\_at|字串|否 |
|repository\_url|字串|否 |


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->