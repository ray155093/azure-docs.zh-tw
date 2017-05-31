---
title: "動態群組與 Azure Active Directory B2B 共同作業 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業可搭配 Azure AD 動態群組使用"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/04/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: a694d01281cfdc4559f779f18b92d0412d59cf45
ms.contentlocale: zh-tw
ms.lasthandoff: 05/05/2017


---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>動態群組與 Azure Active Directory B2B 共同作業

## <a name="what-are-dynamic-groups"></a>什麼是動態群組？
[Azure 入口網站](https://portal.azure.com)允許您動態設定 Azure Active Directory (Azure AD) 的安全性群組成員資格。 系統管理員可以設定規則以根據使用者屬性 (例如 userType、部門或國家/地區) 填入在 Azure Active Directory 中建立的群組。 這允許系統自動根據成員屬性變更在安全性群組中新增或移除成員。 這些群組可用於存取應用程式或雲端資源 (例如 SharePoint 網站和文件)，以及將授權指派給成員。 若要深入了解動態群組，請參閱 [Azure Active Directory 中的專用群組](active-directory-accessmanagement-dedicated-groups.md)。

透過 AAD 進階 P1 或 P2 訂用帳戶，Azure 入口網站現在可讓您建立進階規則，來為 Azure Active Directory 群組啟用更複雜的屬性型動態成員資格。 若要深入了解如何建立進階規則，請參閱[在 Azure Active Directory 中使用屬性來建立群組成員資格的進階規則](active-directory-groups-dynamic-membership-azure-portal.md)。

## <a name="what-are-the-built-in-dynamic-groups"></a>什麼是內建動態群組？
「所有使用者」動態群組讓租用戶系統管理員按一下滑鼠即可建立包含租用戶中所有使用者的群組。 根據預設值，「所有使用者」群組包括目錄中的所有使用者 (包括「成員」與「來賓」)。
在新的 Azure Active Directory 系統管理員入口網站中，您可以選擇在 [群組設定] 檢視中啟用「所有使用者」群組。

![內建群組](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

強化「所有使用者」動態群組 根據預設值，「所有使用者」群組也包含您的 B2B 共同作業 (來賓) 使用者。 若要透過移除來賓使用者以進一步保護您的「所有使用者」群組，您可以透過修改「所有使用者」群組的屬性型規則來輕鬆完成。 下圖顯示已修改為排除來賓的「所有使用者」群組。

![啟用所有使用者群組](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

您可能也會發現建立只包含來賓使用者的新動態群組非常實用。 在想要將原則 (例如「條件式存取」原則) 的目標設定為「來賓」使用者時，這非常實用。
下圖顯示此類群組看起來可能樣子：

![排除來賓使用者](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2B 共同作業邀請](active-directory-b2b-delegate-invitations.md)
* [B2B 共同作業程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
* [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
* [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業使用者宣告對應](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)

