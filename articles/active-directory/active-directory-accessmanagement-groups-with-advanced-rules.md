---
title: "根據 Azure Active Directory 中的使用者屬性以動態方式填入群組 | Microsoft Docs"
description: "說明如何建立群組成員資格的進階規則，包括支援的運算式規則運算子和參數。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: b0c8eb46b6c01662f0b53213843f8a7ad295e5aa
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="populate-groups-dynamically-based-on-user-attributes"></a>根據使用者屬性以動態方式填入群組 
Azure 傳統入口網站可讓您對 Azure Active Directory (Azure AD) 群組啟用更複雜的屬性型動態成員資格。  

當使用者的任何屬性變更時，系統會評估目錄中的所有動態群組規則，以查看使用者的屬性變更是否會觸發任何的群組新增或移除。 如果使用者滿足群組規則，則使用者會新增為該群組的成員。 如果他們不再滿足其所屬群組的規則，則會從該群組的成員中移除。

> [!NOTE]
> 您可以為安全性群組或 Office 365 群組的動態成員資格設定規則。 
>
> 群組的動態成員資格需要將 Azure AD Premium 授權指派給：
>
> * 負責管理群組規則的系統管理員
> * 群組的所有成員
>
> 另請注意，雖然您可以為裝置或使用者建立動態群組，但無法建立規則來同時選取使用者和裝置物件。 

## <a name="to-create-the-advanced-rule"></a>建立進階規則
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，選取 [Active Directory]，然後開啟您組織的目錄。
2. 選取 [群組]  索引標籤，然後開啟您想要編輯的群組。
3. 選取 [設定] 索引標籤，選取 [進階的規則] 選項，然後在文字方塊中輸入進階規則。

## <a name="constructing-the-body-of-an-advanced-rule"></a>建構進階規則的主體
您可以為群組的動態成員資格建立的進階規則基本上是一個二進位運算式，其中包含三個部分，且會產生 true 或 false 的結果。 這三個部分包括：

* 左側的參數
* 二進位運算子
* 右側的常數

完整的進階規則外觀如下：(leftParameter binaryOperator "RightConstant")，其中需要左右括號括住整個二進位運算式、需要雙引號括住右側的常數，且左側參數的語法是 user.property。 進階規則可以包含多個二進位運算式，並以 -and、 -or 和 -not 邏輯運算子分隔。
以下是正確建構的進階規則的範例：

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

如需支援的參數和運算式規則運算子的完整清單，請參閱下列各節。

請注意，屬性前面必須加上正確的物件類型︰使用者或裝置。
以下規則會驗證失敗︰mail –ne null

正確的規則會是︰

user.mail –ne null

進階規則主體的總長度不得超過 2048 個字元。

> [!NOTE]
> 字串和 regex 運算都不區分大小寫。
> 包含引號 " 的字串應該使用 ' 字元逸出，例如 user.department -eq \`"Sales"。
> 只能將引號用於字串類型值，而且只能使用英文引號。
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

## <a name="operator-precedence"></a>運算子優先順序

所有運算子會依照優先順序 (從低至高) 列在底下，同一行中的運算子具有相等的優先順序 -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch

不管有沒有連字號前置詞，均可使用所有運算子。

請注意，不一定需要括號，您只需要在優先順序不符合您的需求時加上括號，例如︰

   user.department –eq "Marketing" –and user.country –eq "US"

相當於：

   (user.department –eq "Marketing") –and (user.country –eq "US")

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

## <a name="use-of-null-values"></a>使用 Null 值

若要在規則中指定 null 值，您可以使用 "null" 或 $null。 範例：

   user.mail –ne null is equivalent to user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>擴充屬性和自訂屬性
動態成員資格規則支援擴充屬性和自訂屬性。

擴充屬性會從內部部署 Windows Server AD 進行同步處理，並採用 "ExtensionAttributeX" 格式，其中 X 等於 1-15。
以下是使用擴充屬性的規則範例：

(user.extensionAttribute15 -eq "Marketing")

自訂屬性會從內部部署 Windows Server AD 或從連接的 SaaS 應用程式進行同步處理，並採用 "user.extension[GUID]\__[Attribute]" 格式，其中 [GUID] 是 AAD 中的唯一識別碼 (適用於在 AAD 中建立屬性的應用程式)，而 [Attribute] 是其建立的屬性名稱。
以下是使用自訂屬性的規則範例：

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

使用 [Graph 總管] 查詢使用者的屬性並搜尋屬性名稱，即可在目錄中找到自訂屬性名稱。

## <a name="support-for-multi-value-properties"></a>支援多重值屬性

若要在規則中包含多值屬性，請使用 "-any" 運算子，如下所示：

  user.assignedPlans -any assignedPlan.service -startsWith "SCO"

## <a name="direct-reports-rule"></a>屬下規則
您可以根據使用者的經理屬性在群組中填入成員。

**設定群組為「經理」群組**

1. 在 Azure 傳統入口網站中，按一下 [Active Directory] ，然後按一下您組織的目錄名稱。
2. 選取 [群組]  索引標籤，然後開啟您想要編輯的群組。
3. 選取 [設定] 索引標籤，然後選取 [進階規則]。
4. 使用下列語法輸入規則：

    *Direct Reports for {obectID_of_manager}* 的 Direct Reports。 以下是 Direct Reports 的有效規則範例：

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    其中 “62e19b97-8b3d-4d4a-a106-4ce66896a863” 為管理員的 objectID。 您可以在 Azure AD 中，身為管理員之使用者的使用者頁面的 [設定檔]  索引標籤上找到物件識別碼。
5. 儲存這項規則時，符合規則的所有使用者都會加入成為群組的成員。 一開始填入群組可能需要幾分鐘的時間。

## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用屬性來建立裝置物件的規則
您也可以建立規則以在群組中選取成員資格的裝置物件。 可以使用下列裝置屬性︰

| 屬性 | 允許的值 | 使用量 |
| --- | --- | --- |
| displayName |任何字串值 |(device.displayName -eq "Rob Iphone”) |
| deviceOSType |任何字串值 |(device.deviceOSType -eq "IOS") |
| deviceOSVersion |任何字串值 |(device.OSVersion -eq "9.1") |
| isDirSynced |true false null |(device.isDirSynced -eq true) |
| isManaged |true false null |(device.isManaged -eq false) |
| isCompliant |true false null |(device.isCompliant -eq true) |
| deviceCategory |任何字串值 |(device.deviceCategory -eq "") |
| deviceManufacturer |任何字串值 |(device.deviceManufacturer -eq "Microsoft") |
| deviceModel |任何字串值 |(device.deviceModel -eq "IPhone 7+") |
| deviceOwnership |任何字串值 |(device.deviceOwnership -eq "") |
| domainName |任何字串值 |(device.domainName -eq "contoso.com") |
| enrollmentProfileName |任何字串值 |(device.enrollmentProfileName -eq "") |
| isRooted |true false null |(device.isRooted -eq true) |
| managementType |任何字串值 |(device.managementType -eq "") |
| organizationalUnit |任何字串值 |(device.organizationalUnit -eq "") |
| deviceId |有效的 deviceId |(device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d" |

> [!NOTE]
> 無法在 Azure 傳統入口網站中使用 [簡單規則] 下拉式清單建立這些裝置規則。
>
>

## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 的其他資訊。

* [疑難排解群組的動態成員資格](active-directory-accessmanagement-troubleshooting.md)
* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)
* [設定群組設定的 Azure Active Directory Cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

