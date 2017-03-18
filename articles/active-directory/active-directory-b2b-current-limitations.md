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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0e71a840d4f503779131ee4a21fe6063d33185f1
ms.openlocfilehash: cbefca2d45a332cd57cfea49dfeaa300426d5502
ms.lasthandoff: 02/24/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 共同作業的限制
Azure Active Directory (Azure AD) B2B 共同作業目前受限於本文所述的限制。

## <a name="invitation-apis-are-in-preview"></a>邀請 API 目前處於預覽狀態
API 介面是我們預期邁向的方向。 不過，就像所有的發行前版本，API 需遵守預覽命名空間合約。 隨著公開上市 (GA) 版本版本的推出，我們會將 API 移至編號版本。

## <a name="possible-double-multi-factor-authentication"></a>可能的雙重 Multi-Factor Authentication
如果您的夥伴已經有 Azure Multi-Factor Authentication 原則，就會發生此備援。 B2B 共同作業 Multi-Factor Authentication 會在邀請組織中執行和管理。 此種驗證令人滿意，因為它涵蓋所有身分識別並可讓您控制 B2B 共同作業受邀者的驗證強度。

然而，如果合作夥伴已設定並強制使用 Multi-Factor Authentication，合作夥伴的使用者可能必須在其所屬組織執行一次驗證，然後在您的組織中再次執行驗證。

在未來版本中，我們打算引進一項原則，以便您選擇信任合作夥伴的 Multi-Factor Authentication 來避免雙重驗證問題。

## <a name="instant-on"></a>Instant-On
在 B2B 共同作業流程中，我們會新增使用者到目錄中，並在邀請兌換與應用程式指派等期間動態更新它們。 更新與寫入通常會在一個目錄執行個體中進行，而且必須複寫到所有執行個體。 我們發現，因為完成複寫的時間有限，有時會引發授權問題。 我們正致力於在正式發行前減少或消除這些問題。 您不太可能同時遇到這些問題，但若是如此，重新整理或重試應有助於解決這些問題。

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

