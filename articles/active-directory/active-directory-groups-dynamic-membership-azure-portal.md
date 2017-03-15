---

title: "Azure Active Directory 預覽版中以屬性為基礎的動態群組成員資格 | Microsoft Docs"
description: "如何建立動態群組成員資格的進階規則，包括支援的運算式規則運算子和參數。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: aa6ab3d2133019203a75b07853908059684bbaae
ms.lasthandoff: 03/08/2017


---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory-preview"></a>在 Azure Active Directory 預覽版中針對動態群組成員資格建立以屬性為基礎的規則
Azure 入口網站可讓您建立進階規則，來為 Azure Active Directory (Azure AD) 預覽版群組啟用更複雜的屬性型動態成員資格。 [預覽版有何功能？](active-directory-preview-explainer.md) 

本文將詳細說明用以建立動態成員資格規則的屬性和語法。

## <a name="to-create-the-advanced-rule"></a>建立進階規則
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [更多服務]，在文字方塊中輸入「使用者和群組」，然後選取 **Enter**。

   ![開啟使用者管理](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)
3. 在 [使用者和群組] 刀鋒視窗上，選取 [所有群組]。

   ![開啟群組刀鋒視窗](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)
4. 在 [使用者和群組 - 所有群組] 刀鋒視窗上，選取 [新增]。

   ![Add new group](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)
5. 在 [群組]  刀鋒視窗上，輸入新群組的名稱和描述。 依據您是要為使用者還是裝置建立規則，在 [成員資格類型] 選取 [動態使用者] 或 [動態裝置]，然後選取 [新增動態查詢]。 如需了解有哪些用於裝置規則的屬性，請參閱 [使用屬性來建立裝置物件的規則](#using-attributes-to-create-rules-for-device-objects)。

   ![新增動態成員資格規則](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)
6. 在 [動態成員資格規則] 刀鋒視窗上，於 [新增動態成員資格進階規則] 方塊中輸入您的規則、按 Enter，然後選取刀鋒視窗底部的 [建立]。
7. 選取 [更多服務]  on the  來建立群組。

## <a name="constructing-the-body-of-an-advanced-rule"></a>建構進階規則的主體
您可以為群組的動態成員資格建立的進階規則基本上是一個二進位運算式，其中包含三個部分，且會產生 true 或 false 的結果。 這三個部分包括：

* 左側的參數
* 二進位運算子
* 右側的常數

完整的進階規則外觀如下：(leftParameter binaryOperator "RightConstant")，其中需要左右括號括住整個二進位運算式、需要雙引號括住右側的常數，且左側參數的語法是 user.property。 進階規則可以包含多個二進位運算式，並以 -and、 -or 和 -not 邏輯運算子分隔。

以下是正確建構的進階規則的範例：

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

如需支援的參數和運算式規則運算子的完整清單，請參閱下列各節。 如需了解有哪些用於裝置規則的屬性，請參閱 [使用屬性來建立裝置物件的規則](#using-attributes-to-create-rules-for-device-objects)。

進階規則主體的總長度不得超過 2048 個字元。

> [!NOTE]
> 字串和 regex 運算都不區分大小寫。 您也可以使用 $null 做為常數，執行 Null 檢查，例如，user.department-eq $null。
> 包含引號 " 的字串應該使用 ' 字元逸出，例如 user.department -eq \`"Sales"。
>
>

## <a name="supported-expression-rule-operators"></a>支援的運算式規則運算子
下表列出所有支援的運算式規則運算子及其用於進階規則主體中的語法：

| 運算子 | 語法 |
| --- | --- |
| Not Equals |-ne |
| Equals |-eq |
| Not Starts With |-notStartsWith |
| 開頭為 |-startsWith |
| Not Contains |-notContains |
| Contains |-contains |
| Not Match |-notMatch |
| Match |-match |

## <a name="query-error-remediation"></a>查詢錯誤補救
下表列出可能的錯誤以及其更正方式

| 查詢剖析錯誤 | 錯誤的使用方式 | 更正的使用方式 |
| --- | --- | --- |
| 錯誤：不支援屬性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>屬性應該符合 [支援的屬性清單](#supported-properties)。 |
| 錯誤：屬性不支援運算子。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/>屬性屬於布林類型。 使用上述清單中的布林型別支援的運算子 (-eq 或-ne)。 |
| 錯誤：查詢編譯錯誤。 |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>邏輯運算子應該符合上述支援的屬性清單中的一個屬性。(user.userPrincipalName -match ".*@domain.ext") 或 (user.userPrincipalName -match "@domain.ext$") 規則運算式發生錯誤。 |
| 錯誤：二進位運算式不是正確的格式。 |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>查詢有多個錯誤。 括號不在正確的位置。 |
| 錯誤：設定動態成員資格時發生未知的錯誤。 |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>查詢有多個錯誤。 括號不在正確的位置。 |

## <a name="supported-properties"></a>支援的屬性
以下是您可以在進階規則中使用的所有使用者屬性：

### <a name="properties-of-type-boolean"></a>布林型別的屬性
允許的運算子

* -eq
* -ne

| 屬性 | 允許的值 | 使用量 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true) |
| dirSyncEnabled |true false null |(user.dirSyncEnabled -eq true) |

### <a name="properties-of-type-string"></a>字串類型的屬性
允許的運算子

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch

| 屬性 | 允許的值 | 使用量 |
| --- | --- | --- |
| city |任何字串值或 $null |(user.city -eq "value") |
| country |任何字串值或 $null |(user.country -eq "value") |
| CompanyName | 任何字串值或 $null | (user.CompanyName -eq "value") |
| department |任何字串值或 $null |(user.department -eq "value") |
| displayName |任何字串值 |(user.displayName -eq "value") |
| facsimileTelephoneNumber |任何字串值或 $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任何字串值或 $null |(user.givenName -eq "value") |
| jobTitle |任何字串值或 $null |(user.jobTitle -eq "value") |
| mail |任何字串值或 $null (使用者的 SMTP 位址) |(user.mail -eq "value") |
| mailNickName |任何字串值 (使用者的郵件別名) |(user.mailNickName -eq "value") |
| mobile |任何字串值或 $null |(user.mobile -eq "value") |
| objectId |使用者物件的 GUID |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |任何字串值或 $null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |任何字串值或 $null |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |任何字串值或 $null |(user.sipProxyAddress -eq "value") |
| state |任何字串值或 $null |(user.state -eq "value") |
| streetAddress |任何字串值或 $null |(user.streetAddress -eq "value") |
| surname |任何字串值或 $null |(user.surname -eq "value") |
| telephoneNumber |任何字串值或 $null |(user.telephoneNumber -eq "value") |
| usageLocation |兩個字母的國家 (地區) 代碼 |(user.usageLocation -eq "US") |
| userPrincipalName |任何字串值 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>字串集合類型的屬性
允許的運算子

* -contains
* -notContains

| 屬性 | 允許的值 | 使用量 |
| --- | --- | --- |
| otherMails |任何字串值 |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>擴充屬性和自訂屬性
動態成員資格規則支援擴充屬性和自訂屬性。

擴充屬性會從內部部署 Windows Server AD 進行同步處理，並採用 "ExtensionAttributeX" 格式，其中 X 等於 1-15。
以下是使用擴充屬性的規則範例：

(user.extensionAttribute15 -eq "Marketing")

自訂屬性會從內部部署 Windows Server AD 或從連接的 SaaS 應用程式進行同步處理，並採用 "user.extension[GUID]\__[Attribute]" 格式，其中 [GUID] 是 AAD 中的唯一識別碼 (適用於在 AAD 中建立屬性的應用程式)，而 [Attribute] 是其建立的屬性名稱。
以下是使用自訂屬性的規則範例：

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

使用 [Graph 總管] 查詢使用者的屬性並搜尋屬性名稱，即可在目錄中找到自訂屬性名稱。

## <a name="direct-reports-rule"></a>屬下規則
您現在可以根據使用者的經理屬性在群組中填入成員。

**設定群組為「經理」群組**

1. 依照[建立進階規則](#to-create-the-advanced-rule)中的步驟 1-5 操作，然後在 [成員資格類型] 選取 [動態使用者]。
2. 在 [動態成員資格規則]  刀鋒視窗上，使用下列語法來輸入規則：

    *Direct Reports for {obectID_of_manager}* 的 Direct Reports。 以下是 Direct Reports 的有效規則範例：

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    其中 “62e19b97-8b3d-4d4a-a106-4ce66896a863” 為管理員的 objectID。 您可以在 Azure AD 中，身為管理員之使用者的使用者頁面的 [設定檔]  索引標籤上找到物件識別碼。
3. 儲存這項規則時，符合規則的所有使用者都會加入成為群組的成員。 一開始填入群組可能需要幾分鐘的時間。

## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用屬性來建立裝置物件的規則
您也可以建立規則以在群組中選取成員資格的裝置物件。 可以使用下列裝置屬性︰

| 屬性              | 允許的值                  | 使用量                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| displayName             | 任何字串值                | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | 任何字串值                | (device.deviceOSType -eq "IOS")                             |
| deviceOSVersion         | 任何字串值                | (device.OSVersion -eq "9.1")                                |
| isDirSynced             | true false null                 | (device.isDirSynced -eq "true")                             |
| isManaged               | true false null                 | (device.isManaged -eq "false")                              |
| isCompliant             | true false null                 | (device.isCompliant -eq "true")                             |
| deviceCategory          | 任何字串值                | (device.deviceCategory -eq "")                              |
| deviceManufacturer      | 任何字串值                | (device.deviceManufacturer -eq "Microsoft")                 |
| deviceModel             | 任何字串值                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | 任何字串值                | (device.deviceOwnership -eq "")                             |
| domainName              | 任何字串值                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | 任何字串值                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false null                 | (device.deviceOSType -eq "true")                            |
| managementType          | 任何字串值                | (device.managementType -eq "")                              |
| organizationalUnit      | 任何字串值                | (device.organizationalUnit -eq "")                          |
| deviceId                | 有效的 deviceId                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d" |




## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 中群組的其他資訊。

* [查看現有的群組](active-directory-groups-view-azure-portal.md)
* [建立新群組並新增成員](active-directory-groups-create-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員資格](active-directory-groups-membership-azure-portal.md)
* [管理群組中使用者的動態規則](active-directory-groups-dynamic-membership-azure-portal.md)

