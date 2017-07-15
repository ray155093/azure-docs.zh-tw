---
title: "Azure AD 多層密碼安全性 | Microsoft Docs"
description: "說明 Azure AD 如何強制執行強式密碼，及保護使用者密碼免遭網路罪犯破解。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 32464307ccb082b25538eaa522c1cdedef1ca555
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# Azure AD 密碼安全性多層法
<a id="a-multi-tiered-approach-to-azure-ad-password-security" class="xliff"></a>

本文討論身為使用者或系統管理員的您可以遵循的一些最佳做法，以保護您的 Azure Active Directory (Azure AD) 或 Microsoft 帳戶帳戶。

 > [!NOTE]
 > Azure AD 系統管理員可以使用[重設 Azure Active Directory 中使用者密碼 (英文)](active-directory-users-reset-password-azure-portal.md) 一文中的指導方針重設使用者密碼。
 >
 > 使用者可以使用[忘記 Azure AD 密碼說明 (英文)](active-directory-passwords-update-your-own-password.md) 一文中的指導方針重設自己的密碼。
 >

## 密碼需求
<a id="password-requirements" class="xliff"></a>

Azure AD 併入下列一般方法來保護密碼︰

* 密碼長度需求
* 密碼複雜性需求
* 一般和定期密碼到期

如需在 Azure Active Directory 中重設密碼的相關資訊，請參閱 [IT 專業人員的 Azure AD 自助式密碼重設 (英文) ](active-directory-passwords.md)主題。

## Azure AD 密碼保護
<a id="azure-ad-password-protections" class="xliff"></a>

Azure AD 和 Microsoft 帳戶系統使用業界證實可行的方法，以確保使用者和系統管理員密碼的保護，包括：

* 動態禁用的密碼
* 智慧型密碼鎖定

如需以目前研究為基礎的密碼管理相關資訊，請參閱[密碼指引 (英文)](http://aka.ms/passwordguidance) 白皮書。

### 動態禁用的密碼
<a id="dynamically-banned-passwords" class="xliff"></a>

Azure AD 和 Microsoft 帳戶藉由動態禁用常見密碼來提供密碼保護。 Azure ID Identity Protection 小組會定期分析遭到禁用的密碼清單，並防止使用者選取常用的密碼。 此服務適用於 Azure AD 和 Microsoft 帳戶服務客戶。

建立密碼時，系統管理員務必鼓勵使用者選擇密碼片語，片語中須包含唯一的字母、數字、字元或文字組合。 這種方法有助於讓使用者密碼幾乎不可能遭到入侵，但使用者更容易記住。

#### 密碼漏洞
<a id="password-breaches" class="xliff"></a>

Microsoft 一直努力在網路罪犯發生前加以防範。

Azure AD Identity Protection 小組會持續分析常用的密碼。 網路罪犯也會使用類似的策略來告知其攻擊，例如建立[彩虹表](https://en.wikipedia.org/wiki/Rainbow_table)用來破解密碼雜湊。

Microsoft 會持續分析[資料外洩](https://www.privacyrights.org/data-breaches)，以維護動態更新的禁用密碼清單，進而確保易受攻擊的密碼在成為 Azure AD 客戶的真正威脅之前遭到禁用。 如需目前安全性成果的詳細資訊，請參閱 [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx)。

### 智慧型密碼鎖定
<a id="smart-password-lockout" class="xliff"></a>

當 Azure AD 偵測到潛在網路罪犯嘗試入侵使用者密碼時，我們會使用智慧密碼鎖定來鎖定使用者帳戶。 Azure AD 設計用來判斷與特定登入工作階段相關聯的風險。 接著我們會使用最新的安全性資料，來套用鎖定語意以阻止網路威脅。

如果使用者的 Azure AD 遭到鎖定，其畫面如下所示︰

  ![Azure AD 遭到鎖定](./media/active-directory-secure-passwords/locked-out-azuread.png)

對於其他 Microsoft 帳戶，其畫面如下所示︰

  ![Microsoft 帳戶遭到鎖定](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

如需在 Azure Active Directory 中重設密碼的相關資訊，請參閱 [IT 專業人員的 Azure AD 自助式密碼重設 (英文) ](active-directory-passwords.md)主題。

  >[!NOTE]
  >如果您是 Azure AD 系統管理員，可以使用 [Windows Hello](https://www.microsoft.com/windows/windows-hello) 全然避免使用者建立傳統密碼。
  >

## 後續步驟
<a id="next-steps" class="xliff"></a>

* [如何更新自己的密碼](active-directory-passwords-update-your-own-password.md)
* [Azure 身分識別管理的基本概念](fundamentals-identity.md)
* [密碼重設活動報告](active-directory-passwords-reporting.md)



