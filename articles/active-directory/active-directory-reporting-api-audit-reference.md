<properties
    pageTitle="Azure Active Directory 稽核 API 參考 | Microsoft Azure"
    description="如何開始使用 Azure Active Directory 稽核 API"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# Azure Active Directory 稽核 API 參考

本主題是 Azure Active Directory 報告 API 相關主題集合的一部分。Azure AD 報告提供的 API 可讓您使用程式碼或相關工具來存取稽核資料。本主題的範疇是為您提供有關**稽核 API** 的參考資訊。

請參閱：

- [稽核記錄](active-directory-reporting-azure-portal.md#audit-logs)以取得詳細概念資訊
- [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md) 以取得報告 API 的詳細資訊。

如有相關疑問、問題或意見，請連絡 [AAD 報告協助](mailto:aadreportinghelp@microsoft.com)。


## 誰可以存取資料？

- 具有安全性系統管理員或安全性讀取器角色的使用者

- 全域管理員

- 任何獲得授權存取 API 的應用程式 (只可以根據全域管理員的權限來設定應用程式授權)



## 必要條件

若要透過報告 API 來存取此報告，您必須具有︰

- [Azure Active Directory Free 或更好的版本](active-directory-editions.md)

- 了解[存取 Azure AD 報告 API 的必要條件](active-directory-reporting-api-prerequisites.md)。
 

##存取 API

您可以透過 [Graph 總管](https://graphexplorer2.cloudapp.net)或以程式設計方式使用 PowerShell 來存取此 API。為了讓 PowerShell 正確地解譯 AAD Graph REST 呼叫中使用的 OData 篩選語法，您必須使用倒單引號 (也稱為︰抑音符號) 字元來「逸出」$ 字元。倒單引號字元可做為 [PowerShell 的逸出字元](https://technet.microsoft.com/library/hh847755.aspx)，讓 PowerShell 進行 $ 字元的常值解譯，並且避免將它與 PowerShell 變數名稱 (例如︰$filter) 搞混。

本主題的重點在於 Graph 總管。如需 PowerShell 範例，請參閱此 [PowerShell 指令碼](active-directory-reporting-api-audit-samples.md#powershell-script)。

 
 

## API 端點


您可以使用下列 URI 來存取此 API︰

	https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Azure AD 稽核 API (使用 OData 分頁) 傳回的記錄筆數沒有任何限制。如需報告資料的保留限制，請參閱[報告保留原則](active-directory-reporting-retention.md)。

此呼叫會分批傳回資料。每個批次的上限為 1000 筆記錄。若要取得下一批記錄，請使用 [下一個] 連結。從第一組傳回的資料取得 skiptoken 資訊。略過 Token 位於結果集的結尾。

	https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## 支援的篩選器

您可以篩選形式縮小 API 呼叫所傳回的記錄筆數。對於登入 API 相關資料，支援下列篩選︰

- **$top=<傳回的記錄筆數>** - 限制傳回的記錄筆數。這是一項昂貴的作業。如果您想要傳回數千個物件，則不應該使用此篩選器。
- **$filter=<您的篩選陳述式>** - 根據支援的篩選欄位，指定您關心的記錄類型



## 支援的篩選欄位和運算子

若要指定您關心的記錄類型，您可以建立一個篩選陳述式，該陳述式可包含下列其中一個篩選欄位或其組合︰

- [signinDateTime](#activitydate) - 定義日期或日期範圍
- [activityType](#activitytype) - 定義活動類型
- [activity](#activity) - 將活動定義為字串
- [actor/name](#actorname) - 以動作項目的名稱形式定義動作項目
- [actor/objectid](#actorobjectid) - 以動作項目的識別碼形式定義動作項目
- [actor/upn](#actorupn) - 以動作項目的使用者主體名稱 (UPN) 形式定義動作項目
- [target/name](#targetname) - 以動作項目的名稱形式定義目標
- [target/objectid](#targetobjectid) - 以目標的識別碼形式定義目標
- [target/upn](#targetupn) - 以動作項目的使用者主體名稱 (UPN) 形式定義動作項目




----------

### activityDate

**支援的運算子**：eq、ge、le、gt、lt

**範例**：

	$filter=activityDate ge 2016-01-01T00:00:00Z and activityDate le 2016-01-02T00:00:00Z	

**注意**：

datetime 應採用 UTC 格式

----------

### activityType

**支援的運算子**：eq

**範例**：

	$filter=activityType eq 'User'	

**注意**：

區分大小寫

----------

### 活動

**支援的運算子**：eq、contains、startsWith

**範例**：

	$filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')	

**注意**：

區分大小寫

----------

### actor/name

**支援的運算子**：eq、contains、startsWith

**範例**：

	$filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')	

**注意**：

不區分大小寫

	

----------
### actor/objectId

**支援的運算子**：eq

**範例**：

	$filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'	

----------
### target/name

**支援的運算子**：eq、contains、startsWith

**範例**：

	$filter=target/name eq 'test' or contains(target/name, 'test') or startswith(target/name, 'test')	

**注意**：

不區分大小寫

----------

### target/upn

**支援的運算子**：eq、startsWith

**範例**：

	$filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))	

**注意**：

- 不區分大小寫
- 您需要時在查詢 Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity 時新增完整的命名空間

----------

### target/objectId

**支援的運算子**：eq

**範例**：

	$filter=target/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'	

----------

### actor/upn

**支援的運算子**：eq、startsWith

**範例**：

	$filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')	

**注意**：

- 不區分大小寫
- 您需要時在查詢 Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity 時新增完整的命名空間

----------




## 後續步驟

- 您想要查看篩選過的系統活動範例嗎？ 請查看 [Azure Active Directory 稽核 API 範例](active-directory-reporting-api-audit-samples.md)。

- 您想要深入了解 Azure AD 報告 API 嗎？ 請參閱[開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。

<!---HONumber=AcomDC_0928_2016-->