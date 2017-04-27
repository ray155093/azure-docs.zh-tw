---
title: "Azure Active Directory B2B 共同作業的限制 | Microsoft Docs"
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
ms.date: 04/12/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: cdc951d4e16e7f0df425dba7c33d86255276f526
ms.lasthandoff: 04/13/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 共同作業的限制
Azure Active Directory (Azure AD) B2B 共同作業目前受限於本文所述的限制。

## <a name="possible-double-multi-factor-authentication"></a>可能的雙重多重要素驗證
使用 Azure AD B2B，您便可以在資源組織 (邀請組織) 強制執行多重要素驗證。 若要了解採用此方法的詳細原因，請參閱 [B2B 共同作業使用者的條件式存取](active-directory-b2b-mfa-instructions.md)。 這意謂著如果合作夥伴已設定並強制執行多重要素驗證，該合作夥伴的使用者可能必須在其主要組織執行一次驗證，然後在您的組織中再執行一次驗證。

在未來的版本中，我們打算引進一項原則，讓您可以藉由選擇信任合作夥伴的多重要素驗證，來避免發生雙重驗證的問題。


## <a name="instant-on"></a>即時
在 B2B 共同作業流程中，我們會新增使用者到目錄中，並在邀請兌換與應用程式指派等期間動態更新它們。 更新與寫入通常會在一個目錄執行個體中進行，而且必須複寫到所有執行個體。 複寫可能會需要一些時間才能完成。 有時，當系統在一個目錄執行個體中寫入或更新物件，而將擷取此物件的呼叫負載平衡至另一個執行個體時，這便導致發生授權問題。 我們已進行許多工作來消除或減少這些複寫延遲，但在一些罕見的情況下，仍然可能會發生延遲。 如果發生該情況，請重新整理或重試來提供幫助。 如果您正在使用我們的 API 來撰寫應用程式，則採用某種讓步來重試是可減輕此問題的良好防禦性做法。

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

