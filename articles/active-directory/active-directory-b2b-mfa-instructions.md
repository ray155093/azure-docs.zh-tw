---
title: "Azure Active Directory B2B 共同作業使用者的多重要素驗證 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業支援多重要素驗證 (MF) 以選擇性存取您的公司應用程式"
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
ms.openlocfilehash: 32d0b45080d57712209e0c5a3e5adf981fb4b66e
ms.lasthandoff: 02/17/2017


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>Azure Active Directory B2B 共同作業使用者的多重要素驗證

在此 Azure AD B2B 共同作業公開預覽刷新版中，我們引進了一個功能，讓組織也可以強制為 B2B 共同作業使用者套用多重要素驗證 (MFA)原則。 在此刷新版中，一律會在資源租用戶中強制套用 MFA。

這表示：
1. 公司 A 中的系統管理員或資訊工作者邀請公司 B 的使用者使用公司 A 的應用程式 Foo。
2. 公司 A 中的應用程式 *Foo* 是設定為要求在存取時使用 MFA。
3. 當公司 B 的使用者嘗試從公司 A 租用戶存取應用程式 Foo 時，系統會要求他們完成 MFA 查問，就如同公司 A 的 MFA 原則所要求的一樣。
4. 使用者可以設定其與公司 A 的 MFA，選擇其 MFA 選項
5. 這對任何身分識別 (例如 Azure AD 或 MSA (若公司 B 的使用者使用社交識別碼來驗證)) 都有效
6. 公司 A 將需要擁有支援 MFA 的適當進階 Azure AD SKU。 公司 B 的使用者將從公司 A 取用此授權。
7. 總而言之，發出邀請的租用戶*一律*必須負責來自合作夥伴組織之 B2B 共同作業使用者的 MFA，而不是由合作夥伴組織本身負責 (即使合作夥伴組織具有 MFA 能力)。 在未來的版本中，我們會讓發出邀請的組織信任特定合作夥伴組織的 MFA，而非使用發出邀請之組織的 MFA。

## <a name="setting-up-mfa-for-b2b-collaboration-users"></a>為 B2B 共同作業使用者設定 MFA
若要了解為 B2B 共同作業使用者設定 MFA 有多簡單，請觀看下列影片：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

## <a name="b2b-users-mfa-experience-for-offer-redemption"></a>方案兌換的 B2B 使用者 MFA 體驗
觀看下面的動畫以了解兌換體驗，如下列影片所示：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

## <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B 共同作業使用者的 MFA 重設
目前，系統管理員只能使用 PowerShell Cmdlet 要求 B2B 共同作業使用者重新證明。 因此，若要重設 B2B 共同作業使用者的證明方法，應該使用下列 PowerShell Cmdlet。

> [!NOTE]
> 若要使用新的 Cmdlet，您必須安裝 Azure AD PowerShell V2 模組 (可從下列位置取得： https://www.powershellgallery.com/packages/AzureADPreview)

1. 連接至 Azure AD

  ```
  Connect-MsolService and login
  ```
2. 取得具有證明方法的所有使用者

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  下列是一個範例：

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. 重設特定使用者的 MFA 方法。 接著，您可以使用該 UserPrincipalName 來執行重設命令，以要求 B2B 共同作業使用者再次設定證明方法。 範例：

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
* [針對 Azure Active Directory B2B 共同作業問題進行疑難排解](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業常見問題集 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)

