---
title: "Azure Active Directory B2B 共同作業授權指引 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業不需要 Azure AD 付費授權，但您也可以取得付費功能給 B2B 來賓使用者"
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
ms.date: 06/29/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 972b37e0009b3096691784d785901b0a5585eb4c
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 共同作業授權指引

使用 B2B 共同作業來邀請來賓使用者加入您的 Azure AD 租用戶，讓他們能夠存取您開放使用的 Azure AD 服務和資源。  

邀請 B2B 使用者並將他們指派給 Azure AD 中的應用程式並無須付費。 B2B 共同作業使用者可以免費使用最多 10 個應用程式 (每位來賓使用者) 和 3 份基本報告。 如果來賓使用者在夥伴的 Azure AD 租用戶中獲得任何適當的授權，則他們在您的租用戶中也會獲得授權。

如果您不想讓這些 B2B 來賓使用者存取 Azure AD 付費功能，則他們必須獲得適當的 Azure AD 授權。 具有 Azure AD 付費授權的邀請方租用戶，可以將 B2B 共同作業使用者權限指派給另外五位受邀加入租用戶的來賓使用者。 

## <a name="licensing-examples"></a>授權範例
- 客戶想要邀請 100 位 B2B 共同作業使用者加入其 Azure AD 租用戶。 此客戶指派存取管理和佈建給所有使用者，但其中 50 位使用者還需要 MFA 和條件式存取。 此客戶必須購買 10 個 Azure AD Basic 授權及 10 個 Azure AD Premium P1 授權，才能正確地涵蓋這些 B2B 使用者。 如果客戶打算對 B2B 使用者使用「身分識別保護」功能，則他們必須有 Azure AD Premium P2 授權，才能涵蓋同樣 5:1 比例的受邀使用者。
- 客戶有 10 位目前以 Azure AD「進階 P1」授權的員工。 他們現在想邀請 60 位 B2B 使用者，而這些使用者全部都需多重要素驗證 (MFA)。 依據 5:1 授權規則，此客戶必須至少有 12 個 Azure AD Premium P1 授權，才能涵蓋全部 60 位 B2B 共同作業使用者。 由於他們已經有 10 位員工的 10 個 Premium P1 授權，因此，他們有權使用 Premium P1 功能 (例如 MFA) 來邀請 50 位 B2B 使用者。 所以，在此範例中，他們必須再購買 2 個 Premium P1 授權，才能涵蓋剩餘的 10 位 B2B 共同作業使用者。

> [!NOTE]
> 尚無法直接將授權指派給 B2B 使用者，以啟用這些 B2B 共同作業使用者權限。

擁有邀請方租用戶的客戶，必須決定多少位 B2B 共同作業使用者需要 Azure AD 付費功能。 根據您想要取得哪些 Azure AD 付費功能給來賓使用者而定，您擁有的 Azure AD 付費授權必須足以涵蓋 5:1 比例的 B2B 共同作業使用者。 

## <a name="additional-licensing-details"></a>其他授權詳細資料
- 並不需要將授權實際指派給 B2B 使用者帳戶。 系統會根據 5:1 比例來自動計算和報告授權。
- 如果租用戶中沒有 Azure AD 付費授權，則每位受邀使用者會獲得 Azure AD Free 版本所提供的權限。
- 如果 B2B 共同作業使用者已從其組織獲得 Azure AD 付費授權，他就不會取用邀請方租用戶的其中一個 B2B 共同作業授權。

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>進階討論：當我們使用您的 API 從某個集團組織新增使用者作為「成員」時，有什麼授權考量？
B2B 來賓使用者是從合作夥伴組織受邀來與主辦組織共同作業的使用者。 一般而言，任何其他情況都不符合 B2B 資格，即使是使用 B2B 功能也一樣。 讓我們來特別看看兩個案例：

1. 如果主辦組織使用取用者位址來邀請員工
  1. 此情節不符合我們的授權原則，不建議使用。

2. 如果主辦組織從另一個集團組織新增使用者
  1. 在此案例中是使用 B2B API 來邀請使用者，但此案例不是傳統的 B2B。 在理想情況下，我們應該讓這些組織邀請其他組織使用者作為成員 (我們的 API 允許這麼做)。 在此情況下，必須將授權指派給這些成員，讓他們存取邀請組織中的資源。

  2. 有些組織可能會想要將另一個組織的使用者新增為「來賓」來作為一項原則。 這裡有兩個案例：
      * 集團組織已經使用 Azure AD 且受邀的使用者已在另一個組織中獲得授權：在此情況下，我們不會預期受邀使用者需依循本文件稍早所配置的 1:5 公式。 

      * 集團組織未使用 Azure AD 或沒有適當的授權：在此情況下，請依循本文件稍早所配置的 1:5 公式。

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [針對 Azure Active Directory B2B 共同作業問題進行疑難排解](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)

