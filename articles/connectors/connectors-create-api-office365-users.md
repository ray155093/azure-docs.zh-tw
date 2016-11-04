---
title: 在 Logic Apps 中新增 Office 365 使用者連接器 | Microsoft Docs
description: 搭配 REST API 參數來使用 Office 365 使用者連接器的概觀
services: ''
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# 開始使用 Office 365 使用者連接器
連接至 Office 365 使用者，以取得設定檔、搜尋使用者等等。

> [!NOTE]
> 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。
> 
> 

您可以利用 Office 365 使用者來：

* 根據您從 Office 365 使用者所取得的資料，來建置您的商務流程。
* 使用可取得直屬員工、取得管理員的使用者設定檔等等的動作。這些動作會收到回應，然後輸出能讓其他動作使用的資料。例如，取得某人的直屬員工，然後利用此資訊更新 SQL Azure 資料庫。

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Office 365 使用者連接器提供下列動作。但不包含觸發程序。

| 觸發程序 | 動作 |
| --- | --- |
| None |<ul><li>取得管理員</li><li>取得我的設定檔</li><li>取得直屬員工</li><li>取得使用者設定檔</li><li>搜尋使用者</li></ul> |

所有連接器都支援 JSON 和 XML 格式的資料。

## 建立至 Office 365 使用者的連線
當您將這個連接器新增到邏輯應用程式時，您必須登入您的 Office 365 使用者帳戶，並允許邏輯應用程式連線到您的帳戶。

> [!INCLUDE [建立至 Office 365 使用者連線的步驟](../../includes/connectors-create-api-office365users.md)]
> 
> 

連線建立之後，您需要輸入 Office 365 使用者屬性，像是使用者識別碼。本主題的 **REST API 參考**一節說明這些屬性。

> [!TIP]
> 您可以在其他的邏輯應用程式中，使用這個相同的 Office 365 使用者連線。
> 
> 

## Office 365 使用者 REST API 參考
適用的版本：1.0。

### 取得我的設定檔
擷取目前使用者的設定檔。```GET: /users/me```

這個呼叫沒有參數。

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 202 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 403 |禁止 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### 取得使用者設定檔
擷取特定的使用者設定檔。```GET: /users/{userId}```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| userId |string |yes |路徑 |無 |使用者主體名稱或電子郵件識別碼 |

#### Response
| Name | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 202 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 403 |禁止 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### 取得管理員
擷取指定使用者之管理員的使用者設定檔。```GET: /users/{userId}/manager```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| userId |string |yes |路徑 |無 |使用者主體名稱或電子郵件識別碼 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 202 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 403 |禁止 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### 取得直屬員工
取得直屬員工。```GET: /users/{userId}/directReports```

| 名稱 | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| userId |string |yes |路徑 |無 |使用者主體名稱或電子郵件識別碼 |

#### Response
| 名稱 | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 202 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 403 |禁止 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

### 搜尋使用者
擷取使用者設定檔的搜尋結果。```GET: /users```

| Name | 資料類型 | 必要 | 位於 | 預設值 | 說明 |
| --- | --- | --- | --- | --- | --- |
| searchTerm |string |no |query |無 |搜尋字串 (適用於：顯示名稱、名字、姓氏、郵件、郵件暱稱，及使用者主體名稱) |

#### Response
| Name | 說明 |
| --- | --- |
| 200 |作業已順利完成 |
| 202 |作業已順利完成 |
| 400 |BadRequest |
| 401 |未經授權 |
| 403 |禁止 |
| 500 |內部伺服器錯誤 |
| 預設值 |作業失敗。 |

## 物件定義
#### User：使用者模型類別。
| 屬性名稱 | 資料類型 | 必要 |
| --- | --- | --- |
| DisplayName |string |no |
| GivenName |string |no |
| Surname |string |no |
| Mail |string |no |
| MailNickname |string |no |
| TelephoneNumber |string |no |
| AccountEnabled |布林值 |no |
| 識別碼 |string |yes |
| UserPrincipalName |string |no |
| 系所 |string |no |
| JobTitle |string |no |
| mobilePhone |string |no |

## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

返回 [API 清單](apis-list.md)。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0824_2016-->