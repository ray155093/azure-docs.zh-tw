---
title: "Azure Active Directory B2B 共同作業授權指引 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業會依據您想要為 B2B 共同作業使用者提供的功能而定，要求付費的 Azure AD 授權"
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
ms.date: 04/12/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 4e620f3d76caa25ac0e5afb134f37ffe263935f0
ms.contentlocale: zh-tw
ms.lasthandoff: 04/13/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 共同作業授權指引

Azure Active Directory (Azure AD) B2B 共同作業會將一組精選的現有 Azure AD 功能延伸給獲邀加入 Azure AD 租用戶的來賓使用者使用。 因此，Azure AD B2B 共同作業來賓使用者將會透過 Azure AD 授權來獲得授權，而與現有的「免費」、「基本」及「進階 P1」/「進階 P2」授權層一致，如這裡所示：https://azure.microsoft.com/pricing/details/active-directory/。

邀請 B2B 使用者並將他們指派給 Azure AD 中的應用程式並無須付費。 此外，就 B2B 使用者而言，每一來賓使用者最多還可享有 10 個免費的應用程式和 3 個基本報告，因為他們屬於 Azure AD「免費」層。
凡是透過 B2B 共同作業功能延伸至 B2B 使用者的付費 Azure AD 功能，都必須以 Azure AD 付費授權 (基本、進階 P1 或進階 P2，視將使用的功能而定) 進行授權。 邀請方租用戶透過每個 Azure AD 付費授權將可獲得 5 份 B2B 使用者權限。 也就是說，每個 Azure AD 付費授權除了將 Azure AD 付費功能的權限提供給租用戶中的一位員工使用者之外，現在也會將相同 Azure AD 付費功能的權限提供給額外 5 個受邀加入該租用戶的 B2B 使用者。

## <a name="licensing-examples"></a>授權範例
- 客戶想要邀請 100 位 B2B 使用者加入其 Azure AD 租用戶，並且將針對所有使用者使用「群組」型存取管理和佈建，但其中 50 位使用者還將需要進行 MFA 和條件式存取。 在此情況下，此客戶必須購買 10 個 Azure AD「基本」授權及 10 個 Azure AD「進階 P1」授權，才能正確地涵蓋其所有 B2B 使用者。 同樣地，如果邀請方租用戶打算搭配 B2B 使用者使用「身分識別保護」功能，它就必須要有足夠的 Azure AD「進階 P2」授權來以 5:1 的比例涵蓋所有這些 B2B 使用者。
- 客戶有 10 位目前以 Azure AD「進階 P1」授權的員工。 它們現在想要邀請 60 位 B2B 使用者，這些使用者都將需要進行多重要素驗證 (MFA)。 依據 5:1 授權規則，此客戶必須至少擁有 12 個 Azure AD「進階 P1」授權，才能涵蓋全部 60 個 B2B 共同作業使用者。 由於它們已經有 10 個「進階 P1」授權供其 10 位員工使用，因此它們已經有權邀請 50 位 B2B 使用者來賦予「進階 P1」功能 (例如 MFA)。 所以在此範例中，它們還需要再購買 2 個「進階 P1」授權來涵蓋剩餘的 10 位 B2B 共同作業使用者。

> [!NOTE]
> 沒有任何機能可將授權指派給 B2B 使用者，也不需要這麼做，即可啟用這些 B2B 共同作業使用者權限。

擁有邀請方租用戶的客戶必須負責決定有多少 B2B 共同作業使用者需要使用付費的 Azure AD 功能，而且視這些功能是「基本」、「進階 P1」還是「進階 P2」層級功能而定，客戶必須要有足夠的適當 Azure AD 付費授權數來以 5:1 的比例涵蓋其 B2B 共同作業使用者。 如果公司需要額外的 B2B 共同作業使用者權限，就必須購買所需的 Azure AD 付費授權。

## <a name="additional-licensing-details"></a>其他授權詳細資料
- B2B 共同作業可以根據現有的 Azure AD 版本模型，為不同的 B2B 共同作業使用者提供不同的功能。
- 每個 Azure AD 付費授權將包含給 5 位 B2B 共同作業使用者 (5:1 模型) 的權限。
- 並不需要將授權實際指派給 B2B 使用者帳戶。 系統將會自動計算和報告。
- 如果租用戶中沒有付費的 Azure AD 授權，則每位受邀使用者會獲得 Azure AD「免費」版本所提供的權限。
- 如果 B2B 共同作業使用者已從其組織員工身分獲得付費的 Azure AD 授權，他就不會取用邀請方租用戶的其中一個 B2B 共同作業授權。

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>進階討論：當我們使用您的 API 從某個集團組織新增使用者作為「成員」時，有什麼授權考量？
B2B 來賓使用者是從合作夥伴組織受邀來與主辦組織共同作業的使用者。 一般而言，此情況以外的任何其他情況都不符合 B2B 資格，即使是使用 B2B 功能。 讓我們來特別看看兩個案例：

1. 如果主辦組織使用取用者位址來邀請員工
  1. 這不符合我們的授權原則，因此目前不建議使用。

2. 如果主辦組織從另一個集團組織新增使用者
  1. 這是使用 B2B API 來邀請使用者的情況，但這並不是傳統上的 B2B。 在理想情況下，我們應該讓這些組織邀請其他組織使用者作為成員 (我們的 API 允許這麼做)。 在此情況下，必須將授權指派給這些成員，讓他們存取邀請組織中的資源。

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
* [Azure Active Directory B2B 共同作業常見問題集 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)

