---
title: "Azure Active Directory B2B 共同作業使用者的屬性 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業的使用者屬性可進行設定"
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
ms.date: 02/03/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: ae154e09e3b9ef2182e74b1dc5a0d8da3922b0df


---

# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 共同作業使用者的屬性

## <a name="defining-a-b2b-collaboration-user"></a>定義 B2B 共同作業的使用者
B2B 共同作業是具有 UserType = Guest 的使用者，或是來賓使用者。 這位使用者通常代表來自夥伴組織的使用者，且依預設在邀請目錄中的權限有限。 根據邀請組織的需求，B2B 共同作業使用者可以是下列帳戶狀態之一︰
1. 位於 Azure Active Directory (Azure AD) 的外部執行個體，表示為主機組織中的來賓使用者。 在此情況下，B2B 使用者會使用屬於他的家用租用之 Azure AD 帳戶登入。 如果使用者所屬之外部組織在邀請時不使用 Azure AD，Azure AD 的來賓使用者會在使用者驗證他的電子郵件地址之後履行邀請時「及時」建立。 這也稱為只是及時 (JIT) 租用，或有時稱為病毒式租用。
2. 位於 Microsoft 帳戶，表示為主機組織中的來賓使用者。 在此情況下，來賓使用者會以 Microsoft 帳戶登入。 在 B2B 共同作業的 Azure AD 公用預覽重新整理中，受邀使用者的非 MSA 社交身分識別 (google.com 或類似) 會在優惠兌換期間及時建立為 Microsoft 帳戶。
3. 位於主機組織中的內部部署 Active Directory，與主機組織的 Azure AD 同步處理。 在此發行期間，雲端中這類使用者的 UserType 必須使用 PowerShell 以手動方式變更為來賓。 我們將會在未來版本中支援這個在 Azure AD Connect 的過程中自動完成。
4. 位於具有 UserType = Guest 之主機組織的 AzureAD 中，並有由主機組織管理的認證。

  ![顯示邀請者的姓名縮寫](media/active-directory-b2b-user-properties/redemption-diagram.png)


現在，讓我們來看看狀態 1 中 B2B 共同作業的使用者在 Azure AD 中的外觀。

### <a name="before-invitation-redemption"></a>邀請兌換之前

![優惠兌換之前](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>邀請兌換之後

![優惠兌換之後](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-b2b-collaboration-user"></a>B2B 共同作業使用者的金鑰屬性。

### <a name="usertype"></a>UserType
這個屬性會指出使用者與主機租用的關聯性。 這可以是兩個值︰
- 成員︰主機組織的員工，使用者在公司的薪資。 例如，這是希望能夠存取僅供內部站台使用的使用者。 這位使用者就不會被認為是外部共同作業人員。
- 來賓︰這表示不被認為是公司內部的使用者。 它們可能是外部共同作業人員、合作夥伴、客戶或例如不預期取得 CEO 內部備忘錄或公司好處的類似使用者。

  > ![注意] UserType 與使用者登入的方式，或使用者屬於哪些目錄角色等等無關。 此屬性只是表示使用者與主機組織的關聯性，並可讓組織強制執行任何依此屬性而定之原則。

### <a name="source"></a>來源
使用者的登入方式。

- 受邀使用者︰這位使用者已被邀請，但尚未履行其邀請。

- 外部的 Active Directory︰這位使用者位於外部組織，並使用屬於其他組織的 Azure AD 帳戶進行驗證。 這會對應到上述的狀態 1。

- Microsoft 帳戶：這位使用者位於 MSA，而且使用 Microsoft 帳戶進行驗證。 這會對應到上述的狀態 2。

- Windows Server AD︰這位使用者已從屬於此組織的內部部署 Active Directory 同步處理。 這會對應到上述的狀態 3。

- Azure Active Directory︰這位使用者會使用屬於此組織的 Azure AD 帳戶進行驗證。 這會對應到上述的狀態 4。

  > ![注意] 來源和 UserType 是獨立的屬性。 來源的值不代表特定 UserType。

## <a name="can-b2b-users-be-added-as-members-instead-of-guests"></a>B2B 使用者是否可新增為成員而非來賓？
一般而言，B2B 使用者和來賓使用者的意義相同。 因此，B2B 共同作業使用者依預設是新增為具有 UserType = Guest 的使用者。 不過，在某些情況下，夥伴組織實際上較為主機組織也所屬之較大傘組織的成員。 如果是的話，主機組織可能要將夥伴組織中的使用者視為成員而非來賓。 在此情況下，使用 B2B 邀請管理員 API 來從夥伴組織新增或邀請使用者至主機組織做為成員。

## <a name="filtering-for-guest-users-in-the-directory"></a>在目錄中篩選來賓使用者

![篩選來賓使用者](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="converting-usertype"></a>轉換 UserType
目前，在 PowerShell 中，使用者可以將 UserType 從成員轉換為來賓，反之亦然。 不過，UserType 屬性應該代表使用者與組織的關聯性。 因此，僅在使用者與組織的關聯性變更時才應該變更這個屬性。 如果使用者的關聯性變更時，其他問題的回答方式為，應變更 UPN 嗎？ 使用者是否應該繼續存取他們有權存取的資源？ 是否應指派信箱？ 因此，我們不建議在 PowerShell 中變更 UserType 做為不可部分完成的活動。 此外，我們在未來的版本中會透過 PowerShell 使這個屬性不可變，因此我們不建議採用相依於此值。

## <a name="removing-guest-user-limitations"></a>移除來賓使用者限制
在某些情況下，您要提供來賓使用者更高的權限。 這麼做，您可以將來賓使用者新增至任何角色，並甚至移除目錄中預設的來賓使用者限制，為其提供與成員相同的權限。 繼續閱讀以深入了解。

可以關閉預設來賓使用者限制，如此公司目錄中的來賓使用者可提供一般使用者 (成員) 具有之相同的目錄權限。

![移除來賓使用者限制](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2bB 共同作業邀請](active-directory-b2b-delegate-invitations.md)
* [動態群組與 B2B 共同作業](active-directory-b2b-dynamic-groups.md)
* [B2B 共同作業程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
* [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
* [B2B 共同作業的使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業的使用者宣告對應](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


