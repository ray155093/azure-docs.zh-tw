---
title: GitHub | Microsoft Docs
description: "使用 Azure App Service 建立邏輯應用程式。 GitHub 是 Web 架構的 Git 儲存機制裝載服務。 它提供所有 Git 分散式修訂控制項和來源程式碼管理 (SCM) 功能，也加入自己的功能。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6468b7e9cde47ca4caf9be14d0ad780a431e6f2


---
# <a name="get-started-with-the-github-connector"></a>開始使用 GitHub 連接器
GitHub 是 Web 架構的 Git 儲存機制裝載服務。 它提供所有 Git 分散式修訂控制項和來源程式碼管理 (SCM) 功能，也加入自己的功能。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。 
> 
> 

您可以從建立邏輯應用程式立即開始，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
GitHub 連接器可當成動作使用，它有觸發程序。 所有連接器都支援 JSON 和 XML 格式的資料。 

 GitHub 連接器提供下列動作及/或觸發程序：

### <a name="github-actions"></a>GitHub 動作
您可以採取下列動作：

| 動作 | 說明 |
| --- | --- |
| [CreateIssue](connectors-create-api-github.md#createissue) |建立問題 |

### <a name="github-triggers"></a>GitHub 觸發程序
您可以接聽下列事件：

| 觸發程序 | 說明 |
| --- | --- |
| 開啟問題時 |開啟問題 |
| 關閉問題時 |關閉問題 |
| 指派問題時 |指派問題 |

## <a name="create-a-connection-to-github"></a>建立 GitHub 的連線
若要使用 GitHub 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料︰ 

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 權杖 |是 |提供 GitHub 認證 |

建立連線後，您就可以用它執行動作，並接聽本文所述的觸發程序。 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 
> [!TIP]
> 您可以在其他邏輯應用程式中使用這個連接。
> 
> 

## <a name="reference-for-github"></a>GitHub 的參考
適用的版本：1.0

## <a name="createissue"></a>CreateIssue
建立問題︰建立問題 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| repositoryOwner |string |是 |路徑 |無 |儲存機制擁有者 |
| repositoryName |string |是 |路徑 |無 |儲存機制名稱 |
| issueBasicDetails | |yes |body |無 |問題詳細資料 |

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="issueopened"></a>IssueOpened
開啟問題時︰開啟問題 

```GET: /trigger/issueOpened``` 

這個呼叫沒有參數

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="issueclosed"></a>IssueClosed
關閉問題時︰關閉問題 

```GET: /trigger/issueClosed``` 

這個呼叫沒有參數

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="issueassigned"></a>IssueAssigned
指派問題時︰指派問題 

```GET: /trigger/issueAssigned``` 

這個呼叫沒有參數

#### <a name="response"></a>Response
| 名稱 | 說明 |
| --- | --- |
| 200 |OK |
| 400 |不正確的要求 |
| 401 |未經授權 |
| 403 |禁止 |
| 404 |找不到 |
| 500 |內部伺服器錯誤。 發生未知錯誤 |
| 預設值 |作業失敗。 |

## <a name="object-definitions"></a>物件定義
### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| title |string |是 |
| body |string |是 |
| 受託人 |string |是 |

### <a name="issuedetailsmodel"></a>IssueDetailsModel
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| title |string |是 |
| body |string |是 |
| 受託人 |string |是 |
| number |string |否 |
| state |string |否 |
| created_at |string |否 |
| repository_url |string |否 |

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


