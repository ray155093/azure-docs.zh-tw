---

title: "無邀請將 B2B 共同作業使用者新增至 Azure Active Directory | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業可讓資訊工作者將使用者從其組織新增到 Azure AD 以存取您的公司應用程式"
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
ms.date: 02/10/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0eef684115f9c21ea61502a10576f74cac0ace8e
ms.openlocfilehash: 8fa8ba169ca85c33e52eee4a7cd5b84aa4012673
ms.lasthandoff: 02/11/2017


---

# <a name="add-b2b-collaboration-users-without-an-invitation"></a>在沒有邀請的情況下新增 B2B 共同作業使用者

若邀請者屬於在其中新增使用者之合作夥伴組織目錄中具有列舉權限的角色，則受邀請的使用者會被新增到發出邀請的組織，而不需要取得邀請。

此案例最適用於：

1. 主組織 (例如，WoodGrove) 中的使用者邀請合作夥伴組織 (例如，Sam@litware.com)) 的使用者做為「來賓」。
2. 主組織中的系統管理員設定原則以允許 Sam 識別及新增來自合作夥伴組織 (Litware) 的使用者。
3. 現在 Sam 可以將來自 Litware 的其他使用者新增到 WoodGrove 目錄、群組或應用程式，而不需要兌換邀請。 若 Sam 具有 Litware 中的適當列舉權限，這會自動發生。

### <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權 (英文)](active-directory-b2b-licensing.md)
* [針對 Azure Active Directory B2B 共同作業問題進行疑難排解](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業常見問題集 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

