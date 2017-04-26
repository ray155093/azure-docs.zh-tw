---
title: "Azure Active Directory B2B 共同作業使用者的條件式存取 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業支援多重要素驗證 (MFA) 以對您的公司應用程式進行選擇性存取"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: fac4657e9b78e900c052384e212aa9f3915f970d
ms.lasthandoff: 04/13/2017


---

# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 共同作業使用者的條件式存取

## <a name="multi-factor-authentication-for-b2b-users"></a>B2B 使用者的多重要素驗證
在使用 Azure AD B2B 共同作業的情況下，組織會獨特具備為 B2B 使用者強制執行多重要素驗證原則的能力。 這些原則可以在租用戶層級、應用程式層級或個別使用者層級強制執行，同樣地，這些原則也可以針對組織的全職員工和成員啟用。 MFA 原則會在資源組織強制執行。

這表示：
1. 公司 A 中的系統管理員或資訊工作者邀請公司 B 的使用者使用公司 A 的應用程式 Foo。
2. 公司 A 中的應用程式 *Foo* 是設定為要求在存取時使用 MFA。
3. 當公司 B 的使用者嘗試從公司 A 租用戶存取應用程式 Foo 時，系統會要求他們完成 MFA 查問，就如同公司 A 的 MFA 原則所要求的一樣。
4. 使用者可以設定其與公司 A 的 MFA，選擇其 MFA 選項
5. 這對任何身分識別 (例如 Azure AD 或 MSA (若公司 B 的使用者使用社交識別碼來驗證)) 都有效
6. 公司 A 將需要擁有支援 MFA 的適當進階 Azure AD SKU。 公司 B 的使用者將從公司 A 取用此授權。

總而言之，發出邀請的租用戶*一律*必須負責來自合作夥伴組織之 B2B 共同作業使用者的 MFA，而不是由合作夥伴組織本身負責 (即使合作夥伴組織具有 MFA 能力)。

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>為 B2B 共同作業使用者設定 MFA
若要了解為 B2B 共同作業使用者設定 MFA 有多簡單，請觀看下列影片：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>方案兌換的 B2B 使用者 MFA 體驗
觀看下面的動畫以了解兌換體驗，如下列影片所示：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B 共同作業使用者的 MFA 重設
目前，系統管理員只能使用 PowerShell Cmdlet 要求 B2B 共同作業使用者重新證明。 因此，若要重設 B2B 共同作業使用者的證明方法，應該使用下列 PowerShell Cmdlet。

1. 連接至 Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. 取得具有證明方法的所有使用者

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  下列是一個範例：

  ```
  PS C:\Users\tjwasserGet-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. 重設特定使用者的 MFA 方法。 接著，您可以使用該 UserPrincipalName 來執行重設命令，以要求 B2B 共同作業使用者再次設定證明方法。 範例：

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>為什麼我們要在資源租用執行 MFA？

在目前的版本中，MFA 一律是在資源租用中。 這是為了可預測性。

例如，假設 Contoso 使用者 (Sally) 受邀到 Fabrikam，而且 Fabrikam 已經為 B2B 使用者啟用 MFA。

現在，如果 Contoso 已針對 App1 啟用 MFA 原則，但未針對 App2 啟用該原則 – 則當我們查看權杖中的 Contoso MFA 宣告來判斷 Sally 是否應該在 Fabrikam 中進行 MFA 時，可能會發生以下問題：

* 第 1 天：Sally 已在 Contoso 中進行 MFA，因為她要存取 App1，接著她將不會在 Fabrikam 中看到 MFA 提示。

* 第 2 天：Sally 已在 Contoso 中存取 App2，因此，現在當她存取 Fabrikam 時，她將必須在 Fabrikam 中註冊 MFA。

這對 Sally 來說會相當困擾，而可能導致無法完成登入。

此外，即使 Contoso 具有 MFA 功能，Fabrikam 也未必一定會信任 Contoso MFA 原則。

最後，資源租用戶 MFA 也適用於 MSA 和社交識別碼，以及未設定 MFA 的合作夥伴組織。

因此，針對 B2B 使用者的 MFA 建議是一律要求資源 MFA。 在某些情況下，這可能會導致雙重 MFA，但每次存取資源租用時的使用者體驗都會是可預測的：Sally 必須向資源租用註冊 MFA。

### <a name="device-location-and-risk-based-conditional-access-for-b2b-users"></a>B2B 使用者的裝置、位置及風險型條件式存取

當 Contoso 組織為其公司資料啟用裝置型條件式存取原則時，系統會防止未受管理的裝置 (也就是未受 Contoso 組織管理且未符合 Contoso 裝置原則的裝置) 進行存取。

如果 B2B 使用者的裝置未受 Contoso 管理，這就意謂著當 B2B 使用者從合作夥伴組織進行存取時，只要是在強制執行這些原則的內容中，系統就會封鎖其存取。

期望來自另一個組織的使用者將裝置交給邀請組織管理是一項高標準。 因此，在未來的更新中，我們將會讓 Contoso 能夠信任特定合作夥伴的裝置合規性狀態。 這將允許 Contoso 強制執行原則，以讓來自 Fabrikam 的使用者只要是使用受 Fabrikam 管理的裝置，也能夠存取 Contoso 資源。

在此同時，Contoso 可以建立排除清單，當中包含來自裝置型條件式存取原則的特定合作夥伴使用者。

#### <a name="location-based-conditional-access-for-b2b"></a>B2B 的位置型條件式存取

如果邀請組織 (例如 Contoso) 能夠建立定義其合作夥伴組織 (例如 Fabrikam) 的受信任網路週邊 (也就是 IP 位址範圍)，便可以針對 B2B 使用者強制執行位置型條件式存取原則。

#### <a name="risk-based-conditional-access-for-b2b"></a>B2B 的風險型條件式存取

目前，登入風險型原則無法套用至 B2B 使用者，因為風險評估是在 B2B 使用者的主要組織 (換句話說，就是 B2B 使用者的身分識別租用) 執行。

針對未來的更新，我們考慮聯合來自合作夥伴的風險分數 (在合作夥伴同意下)，以便讓 Contoso 不僅是能夠保護其外部共用應用程式和資料抵禦他們已知的風險，也抵禦他們無法得知的風險，因為這些風險可能發生在其他地方。

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

