---
title: "設定群組設定的 Azure Active Directory Cmdlet | Microsoft Docs"
description: "如何使用 Azure Active Directory Cmdlet 管理群組的設定。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6a8bd076830d9b639007ee0130320869d2a63746


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>設定群組設定的 Azure Active Directory Cmdlet
您可以在目錄中針對整合的群組設定下列設定：

1. 分類︰使用者可以在群組上設定的分類清單 (以逗號分隔)。 範例為「已分類」、「密碼」和「最高機密」。
2. 使用方針 URL：將使用者指向使用整合群組的使用規定 (如您的組織所定義) 的 URL。 這個 URL 會顯示於使用者使用群組的使用者介面中。
3. 已啟用群組建立︰是否允許部分或所有使用者建立整合的群組，或者不允許任何使用者建立。 設為開啟時，所有使用者都可建立群組。 設為關閉時，沒有任何使用者可以建立群組。 關閉時，您也可以指定安全性群組，仍然允許其中的使用者建立群組。

這些設定是使用 Settings 和 SettingsTemplate 物件所設定。 一開始，您將不會在目錄中看到任何 Settings 物件。 這表示已使用預設設定來設定您的目錄。 若要變更預設設定，您將使用設定範本來建立新的設定物件。 設定範本是由 Microsoft 所定義。

您可以從 [Microsoft Connect 網站](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)下載模組，其中包含適用於這些作業的 Cmdlet。

## <a name="create-settings-at-the-directory-level"></a>建立目錄層級的設定
這些步驟會建立目錄層級的設定，其會套用至目錄中的所有 Office 群組。

1. 如果您不知道要使用哪一個 SettingTemplate，這個 Cmdlet 會傳回設定範本的清單：
   
    `Get-MsolAllSettingTemplate`
   
    ![設定範本的清單](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)
2. 若要新增使用方針 URL，首先您需要取得 SettingsTemplate 物件，此物件會定義使用方針 URL 值；也就是 Group.Unified 範本：
   
    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`
3. 接下來，根據該範本建立新的設定物件：
   
    `$setting = $template.CreateSettingsObject()`
4. 然後更新使用方針值：
   
    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`
5. 最後，套用設定：
   
    `New-MsolSettings –SettingsObject $setting`
   
    ![新增使用方針 URL](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

以下是 Group.Unified SettingsTemplate 中定義的設定。

| **設定** | **說明** |
| --- | --- |
|  <ul><li>ClassificationList<li>類型：字串<li>預設值：“” |以逗號分隔的有效分類值清單，這些值可以套用到整合的群組。 |
|  <ul><li>EnableGroupCreation<li>型別：布林值<li> 預設值︰True |此旗標指出是否允許在目錄中建立整合的群組。 |
|  <ul><li>GroupCreationAllowedGroupId<li>類型：字串<li>預設值：“” |安全性群組的 GUID，即使 EnableGroupCreation == false，還是允許建立整合的群組。 |
|  <ul><li>UsageGuidelinesUrl<li>類型：字串<li>預設值：“” |群組使用方針的連結。 |

## <a name="read-settings-at-the-directory-level"></a>讀取目錄層級的設定
這些步驟會讀取目錄層級的設定，其會套用至目錄中的所有 Office 群組。

1. 讀取所有現有的目錄設定：
   
    `Get-MsolAllSettings`
2. 讀取特定群組的所有設定：
   
    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`
3. 使用 SettingId GUID 讀取特定的目錄設定：
   
    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
   
    ![設定識別碼 GUID](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>更新目錄層級的設定
這些步驟會更新目錄層級的設定，其會套用至目錄中的所有 Office 群組。

1. 取得現有的 Settings 物件：
   
    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
2. 取得您想要更新的值：
   
    `$value = $Setting.GetSettingsValue()`
3. 更新值：
   
    `$value["AllowToAddGuests"] = "false"`
4. 更新設定：
   
    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>移除目錄層級的設定
這個步驟會移除目錄層級的設定，其會套用至目錄中的所有 Office 群組。

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Cmdlet 語法參考
您可以在 [Azure Active Directory Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=808260)中找到更多 Azure Active Directory PowerShell 文件。

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>SettingsTemplate 物件參考 (Group.Unified SettingsTemplate 物件)
* 「名稱」："EnableGroupCreation"、「類型」："System.Boolean"、「defaultValue」："true"、「說明」："一個布林旗標，指出是否已開啟整合的群組建立功能。"
* 「名稱」："GroupCreationAllowedGroupId"、「類型」："System.Guid"、「defaultValue」：""、「說明」："安全性群組的 GUID，其會在可建立整合群組的允許清單中。"
* 「名稱」："ClassificationList"、「類型」："System.String"、「defaultValue」：""、「說明」："以逗號分隔的有效分類值清單，這些值可以套用到整合的群組。"
* 「名稱」："UsageGuidelinesUrl"、「類型」："System.String"、「defaultValue」：""、「說明」："群組使用方針的連結。"

| 名稱 | 類型 | defaultValue | 說明 |
| --- | --- | --- | --- |
| "EnableGroupCreation" |"System.Boolean" |"true" |"一個布林旗標，指出是否已開啟整合的群組建立功能。" |
| "GroupCreationAllowedGroupId" |"System.Guid" |"" |"安全性群組的 GUID，其會在可建立整合群組的允許清單中。" |
| "ClassificationList" |"System.String" |"" |"以逗號分隔的有效分類值清單，這些值可以套用到整合的群組。" |
| "UsageGuidelinesUrl" |"System.String" |"" |"群組使用方針的連結。" |

## <a name="next-steps"></a>後續步驟
您可以在 [Azure Active Directory Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=808260)中找到更多 Azure Active Directory PowerShell 文件。

如需 Microsoft 專案經理 Rob de Jong 提供的其他指示，請參閱 [Rob 的群組部落格](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad)。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)




<!--HONumber=Nov16_HO3-->


