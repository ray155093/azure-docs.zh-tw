---
title: "Azure AD Connect︰當您已經有 Azure AD 時 | Microsoft Docs"
description: "本主題描述當您有現有的 Azure AD 租用戶時，如何使用 Connect。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: f59028a2f909914222236f3b3575afd0949b4277
ms.openlocfilehash: c89e206462856d25a81729e7028065ac1cd13ef3
ms.contentlocale: zh-tw
ms.lasthandoff: 02/23/2017

---

# Azure AD Connect︰當您有存在的租用戶
<a id="azure-ad-connect-when-you-have-an-existent-tenant" class="xliff"></a>
大部分說明如何使用 Azure AD Connect 的主題會假設您開始使用新的 Azure AD 租用戶，而且沒有任何使用者或其他物件。 但如果您開始使用 Azure AD 租用戶，且以使用者和其他物件填入，而現在想要使用 Connect，則這個主題很適用於您。

## 基本概念
<a id="the-basics" class="xliff"></a>
Azure AD 中的物件可能會在雲端 (Azure AD) 中受控或內部部署。 針對一個單一物件，您無法管理內部部署的某些屬性和 Azure AD 中的一些其他屬性。 每個物件都有表示物件受管理位置的旗標。

您可以在內部部署管理部分使用者，並在雲端中管理其他使用者。 混合著會計工作者和銷售工作者的組織是此組態的常見案例。 會計工作者具有內部部署 AD 帳戶，但銷售工作者沒有這種帳戶，他們在 Azure AD 中有帳戶。 您可在內部部署管理部分使用者，並在 Azure AD 管理其他使用者。

如果您開始管理 Azure AD 且也在內部部署 AD 中的使用者，且稍後想要使用 Connect，則您必須考慮一些其他考量。

## 在 Azure AD 中與現有的使用者同步處理
<a id="sync-with-existing-users-in-azure-ad" class="xliff"></a>
當您安裝 Azure AD Connect 時，您開始同步處理，Azure AD 同步服務 (在 Azure AD 中) 會檢查每個新的物件，然後嘗試尋找要比對的現有物件。 有三種屬性用於此處理程序︰**userPrincipalName**、**proxyAddresses** 和 **sourceAnchor**/**immutableID**。 比對 **userPrincipalName** 和 **proxyAddresses** 稱為**大致比對**。 比對 **sourceAnchor** 稱為**精確比對**。 針對 **proxyAddresses** 屬性，僅具有 **SMTP:** 的值 (也就是主要電子郵件地址) 會用來進行評估。

比對只會評估來自 Connect 的新物件。 如果您變更現有的物件，而它會比對任何這些屬性，則您會看到錯誤。

如果 Azure AD 找到的物件當中屬性值與來自 Connect 的物件相同，且已經存在於 Azure AD，則 Azure AD 中的物件會接替 Connect。 先前的雲端管理的物件標示為內部部署管理。 Azure AD 中具有內部部署 AD 值的所有屬性會以內部部署值覆寫。 例外狀況是在屬性擁有 **NULL** 值的內部部署。 在此情況下，會保留 Azure AD 的值，但是您仍然只能在內部部署將它變更為其他項目。

> [!WARNING]
> 因為 Azure AD 中的所有屬性都將會以內部部署值覆寫，請確定您有良好的資料在內部部署。 例如，如果您只在 Office 365 中管理電子郵件地址，且不會在內部部署 AD DS 中保持更新，則您會遺失不存在於 AD DS 之 Azure AD/Office 365 中的任何值。

> [!IMPORTANT]
> 如果您使用一律會使用快速設定的密碼同步，則會使用內部部署 AD 中的密碼覆寫 Azure AD 中的密碼。 如果您的使用者用來管理不同的密碼，則您需要通知它們在您已安裝 Connect 時，應該使用內部部署密碼。

必須在您的規劃中考量上一節和警告。 如果您在 Azure AD 中進行的許多變更不會在內部部署 AD DS 中反映，則您需要規劃如何在使用 Azure AD Connect 同步您的物件之前填入 AD DS 與更新的值。

如果以大致比對來比對軟體，則 **sourceAnchor** 會新增至 Azure AD 中的物件，以便稍後可以使用精確比對。

### 精確比對與大致比對
<a id="hard-match-vs-soft-match" class="xliff"></a>
對於 Connect 的全新安裝，精確比對與大致比對之間並無實際差別。 不同之處在於災害復原狀況。 如果遺失您的伺服器與 Azure AD Connect，您可以重新安裝新的執行個體而不會遺失任何資料。 在初始安裝期間，具有 sourceAnchor 的物件會傳送到 Connect。 接著，用戶端 (Azure AD Connect) 可以評估比對，這會比在 Azure AD 中執行相同動作更快速。 精確比對會由 Connect 和 Azure AD 評估。 大致比對只會由 Azure AD 評估。

### 使用者以外的其他物件
<a id="other-objects-than-users" class="xliff"></a>
使用者通常會有 userPrincipalName 和 proxyAddresses，使比對更容易。 但是其他物件 (例如安全性群組) 沒有這些。 在此情況下，您只可以使用 sourceAnchor 比對精確比對。 SourceAnchor 一律為 Base64 轉換的 **objectGUID**內部部署，因此當您需要比對兩個物件時，必須更新 Azure AD 中的值。 sourceAnchor/immutableID 僅可使用 PowerShell 而無法透過入口網站更新。

## 從 Azure AD 中的資料建立新的內部部署 Active Directory
<a id="create-a-new-on-premises-active-directory-from-data-in-azure-ad" class="xliff"></a>
有些客戶開始使用僅限雲端的解決方案與 Azure AD，且他們沒有內部部署 AD。 稍後，他們要使用內部部署資源，而且想要根據 Azure AD 資料建置內部部署 AD。 Azure AD Connect 無法針對此案例協助您。 它不會在內部部署建立使用者，且它無法在內部部署將密碼設定為與 Azure AD 中的相同。

如果您計劃要新增內部部署 AD 的唯一理由是要支援 LOB (特定業務應用程式)，則或許您應該考慮改為使用 [Azure AD 網域服務](../../active-directory-domain-services/index.md)。

## 後續步驟
<a id="next-steps" class="xliff"></a>
深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

