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
ms.date: 02/01/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 2d6295d643207f9aef31a88515668b99b51c49de
ms.openlocfilehash: 28abb802f0581e504e8d3eb729f0ea5fffac3d20


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 共同作業授權指引

Azure Active Directory (Azure AD) B2B 共同作業會將一組精選的現有 Azure AD 功能延伸給獲邀加入 Azure AD 租用戶的來賓使用者使用。 因此，Azure AD B2B 共同作業來賓使用者將會透過 Azure AD 授權來獲得授權，而與現有的「免費」、「基本」及「進階 P1/「進階 P2」授權層一致，如這裡所示：https://azure.microsoft.com/en-us/pricing/details/active-directory/。

邀請 B2B 使用者並將他們指派給 Azure AD 中的應用程式並無須付費。 此外，就 B2B 使用者而言，每一來賓使用者最多還可享有 10 個免費的應用程式和 3 個基本報告，因為他們屬於 Azure AD「免費」層。
凡是透過 B2B 共同作業功能延伸至 B2B 使用者的付費 Azure AD 功能，都必須以 Azure AD 付費授權 (基本、進階 P1 或進階 P2，視將使用的功能而定) 進行授權。 邀請方租用戶透過每個 Azure AD 付費授權將可獲得 5 份 B2B 使用者權限。 也就是說，每個 Azure AD 付費授權除了提供權限給租用戶中的一位員工使用者之外，現在也包括給受邀加入該租用戶之 5 位 B2B 使用者的權限。

## <a name="licensing-examples"></a>授權範例
- 客戶想要邀請 100 位 B2B 使用者加入其 Azure AD 租用戶，並且將針對所有使用者使用「群組」型存取管理和佈建，但其中 50 位使用者還將需要進行 MFA 和條件式存取。 在此情況下，此客戶必須購買 10 個 Azure AD「基本」授權及 10 個 Azure AD「進階 P1」授權，才能正確地涵蓋其所有 B2B 使用者。 同樣地，如果邀請方租用戶打算搭配 B2B 使用者使用「身分識別保護」功能，它就必須要有足夠的 Azure AD「進階 P2」授權來以 5:1 的比例涵蓋所有這些 B2B 使用者。
- 客戶有 10 位目前以 Azure AD「進階 P1」授權的員工。 它們現在想要邀請 60 位 B2B 使用者，這些使用者都將需要進行多重要素驗證 (MFA)。 依據 5:1 授權規則，此客戶必須至少擁有 12 個 Azure AD「進階 P1」授權，才能涵蓋全部 60 個 B2B 共同作業使用者。 由於它們已經有 10 個「進階 P1」授權供其 10 位員工使用，因此它們已經有權邀請 50 位 B2B 使用者來賦予「進階 P1」功能 (例如 MFA)。 所以在此範例中，它們還需要再購買 2 個「進階 P1」授權來涵蓋剩餘的 10 位 B2B 共同作業使用者。

> [!NOTE]
沒有任何機能可將授權指派給 B2B 使用者，也不需要這麼做，即可啟用這些 B2B 共同作業使用者權限。

擁有邀請方租用戶的客戶必須負責決定有多少 B2B 共同作業使用者需要使用付費的 Azure AD 功能，而且視這些功能是「基本」、「進階 P1」還是「進階 P2」層級功能而定，客戶必須要有足夠的適當 Azure AD 付費授權數來以 5:1 的比例涵蓋其 B2B 共同作業使用者。 如果公司需要額外的 B2B 共同作業使用者權限，就必須購買所需的 Azure AD 付費授權。

## <a name="additional-licensing-details"></a>其他授權詳細資料
- B2B 共同作業可以根據現有的 Azure AD 版本模型，為不同的 B2B 共同作業使用者提供不同的功能。
- 每個 Azure AD 付費授權將包含給 5 位 B2B 共同作業使用者 (5:1 模型) 的權限。
- 並不需要將授權實際指派給 B2B 使用者帳戶。 系統將會自動計算和報告。
- 如果租用戶中沒有付費的 Azure AD 授權，則每位受邀使用者會獲得 Azure AD「免費」版本所提供的權限。
- 如果 B2B 共同作業使用者已從其組織員工身分獲得付費的 Azure AD 授權，他就不會取用邀請方租用戶的其中一個 B2B 共同作業授權。

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


