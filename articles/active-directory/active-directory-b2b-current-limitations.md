---
title: "Azure Active Directory B2B 共同作業目前的限制 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業預覽的目前限制"
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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 4dae74740d8fd00d36492e5a9fad8848b5d03952
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-current-limitations"></a>Azure Active Directory B2B 共同作業目前的限制

1. Azure Active Directory (Azure AD) B2B 共同作業邀請 API 目前正在進行搶鮮版 (Beta) 測試。該 API 介面是正向，不過，與所有搶鮮版相同，需受搶鮮版命名空間合約規範。 隨著正式版本的推出，我們會將 API 移至編號版本。
2. 若您的合作夥伴已部署 MFA 原則，將在發出邀請的組織執行並管理 B2B 共同作業，因此可能會發生雙重多重要素驗證 (MFA)。 這是預期行為，因為它涵蓋所有身分識別且可讓您控制 B2B 共同作業受邀者的驗證強度。 然而，若您有已設定並強制使用 MFA 的合作夥伴，其使用者可能必須在其所屬組織執行一次 MFA，然後在您的組織中再次執行 MFA。 在未來的版本中，我們將引進一個原則，讓您可以選擇信任特定合作夥伴的 MFA 以避免雙重 MFA 問題。
3. Instant ON 在 B2B 共同作業流程中，我們會新增使用者到目錄中，並在邀請兌換與應用程式指派等期間動態更新它們。 更新與寫入一般會在一個目錄執行個體中完成，而且必須複寫到所有執行個體。 我們發現在某些案例中，完成複寫的有限時間會導致歸類為授權問題的問題。 我們正致力於在正式發行前減少/消除此類問題。 同時，您應該不會遇到此問題，但若您不幸遇到，重新整理或再試一次通常會有幫助。

  ## <a name="next-steps"></a>後續步驟

  請瀏覽有關 Azure AD B2B 共同作業的其他文章：

  * [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
  * [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
  * [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
  * [委派 B2B 共同作業邀請](active-directory-b2b-delegate-invitations.md)
  * [動態群組與 B2B 共同作業](active-directory-b2b-dynamic-groups.md)
  * [B2B 共同作業程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
  * [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
  * [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
  * [B2B 共同作業使用者宣告對應](active-directory-b2b-claims-mapping.md)
  * [Office 365 外部共用](active-directory-b2b-o365-external-user.md)

