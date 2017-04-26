---
title: "Azure Active Directory B2B 共同作業常見問題集 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業相關的常見問題集"
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
ms.date: 04/13/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c06e8b98fea6036704b07b0f12397fc4bb59cb3f
ms.lasthandoff: 04/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Azure Active Directory B2B 共同作業常見問題集 (FAQ)

常見問題集會定期更新以反映新的興趣。

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>此功能是否可在 Azure 傳統入口網站中取得？
Azure AD B2B 共同作業的新功能僅透過 [Azure 入口網站](https://portal.azure.com)和新的「存取面板」提供。 

### <a name="is-it-possible-to-customize-our-sign-in-page-so-its-more-intuitive-for-your-b2b-collaboration-guest-users"></a>是否可以自訂我們的登入頁面，以便為 B2B 共同作業來賓使用者提供更直覺式的頁面？
當然！ 有一篇[說明此功能的部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)，並且於[在登入和存取面板頁面新增公司商標](active-directory-add-company-branding.md)中也有記載。

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B 共同作業的使用者可以存取 SharePoint Online 和 OneDrive 嗎？
是。 不過，在 SharePoint Online 人員選擇器中搜尋現有來賓使用者的功能預設為關閉，以符合舊版的行為。 您可以使用租用戶和網站集合層級中的 'ShowPeoplePickerSuggestionsForGuestUsers' 設定來啟用此功能。 您可以使用 Set-SPOTenant 和 Set-SPOSite Cmdlet 來設定此功能，讓成員搜尋目錄中所有的現有來賓使用者。 租用戶範圍中的變更不會影響已佈建的 SharePoint Online 網站。

### <a name="is-the-csv-upload-mechanism-still-supported"></a>是否仍支援 CSV 上傳機制？
是。 請參閱我們已包含的 [PowerShell 範例](active-directory-b2b-code-samples.md)。

### <a name="how-can-i-customize-my-invitation-emails"></a>如何自訂我的邀請電子郵件？
您可以使用 [B2B 邀請 API](active-directory-b2b-api.md) 來自訂幾乎所有關於邀請者程序的項目。

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>受邀的外部使用者是否可以離開他已受邀的組織？
目前未提供此功能。

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>現在，Multi-Factor Authentication (MFA) 可供來賓使用者使用，他們是否也可以重設其 MFA 方法？
是的，一般使用者可以的相同方式。

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>什麼公司負責 MFA 授權？
邀請組織是介入並執行 MFA 的組織。 因此，邀請組織負責確定它們有足夠的授權可提供給執行 MFA 之 B2B 使用者。

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>如果我的合作夥伴組織已設定 MFA 會如何？ 我們可以信任其 MFA 且不使用我們的 MFA 嗎？
我們將在未來的版本中提供這項支援。 發行時，您將能夠選取要從您的 (邀請組織的) MFA 排除之特定協力廠商。

### <a name="how-can-i-achieve-delayed-invitations"></a>如何達成延遲的邀請？
有些組織想要新增 B2B 共同作業使用者、將它們佈建至需要佈建的應用程式，然後送出邀請。 如果您是，您可以使用 B2B 共同作業邀請 API 自訂登入工作流程。

### <a name="can-i-make-my-guest-users-limited-admins"></a>我是否可以為來賓使用者提供有限的系統管理員權限？
當然。 如果這是您的組織所需，請在[將來賓使用者新增至角色](active-directory-users-assign-role-azure-portal.md)中了解如何進行。

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B 共同作業是否支援允許 B2B 使用者存取 Azure 入口網站？
B2B 共同作業的使用者應該不需要存取 Azure 入口網站，除非他們獲指派有限的系統管理員或全域管理員角色。 在此情況下，他們可以存取入口網站。 如果不是這些角色中的來賓使用者存取入口網站，則他/她可能無法存取經驗的某些部分，因為來賓使用者角色在目錄中擁有特定權限，如先前幾節中所述。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>我可以封鎖來賓使用者存取 Azure 入口網站嗎？
可以！ 但是當您設定此原則以避免不小心封鎖存取成員和系統管理員時要小心。
您可以執行下列三個步驟，透過 Windows Azure 服務管理 API 的條件式存取原則封鎖來賓使用者存取 [Azure 入口網站](https://portal.azure.com)。
1. 修改 [所有使用者] 群組，使其僅包含 [成員] ![修改群組螢幕擷取畫面](media/active-directory-b2b-faq/modify-all-users-group.png)
2. 建立包含來賓使用者的動態群組 ![建立群組螢幕擷取畫面](media/active-directory-b2b-faq/group-with-guest-users.png)
3. 設定條件式存取原則以防止來賓使用者存取入口網站，如下列影片中所示。
  
  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Azure AD B2B 共同作業會根據哪一個時間表啟動 MFA 和取用者電子郵件帳戶的支援？
現在已同時支援 MFA 和取用者電子郵件帳戶。

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>是否有支援 Azure AD B2B 共同作業使用者密碼重設的計劃？
是。 以下是關於 B2B 使用者之自助式密碼重設 (SSPR) 的詳細資料，其中此 B2B 使用者是從其身分識別租用受邀至資源租用：
 
* SSPR 只會在 B2B 使用者的身分識別租用中發生
* 如果身分識別租用是 Microsoft 帳戶，就會使用 Microsoft 帳戶 SSPR 機制
* 如果身分識別租用是及時 (Just In Time)/病毒式租用，將會傳送重設電子郵件
* 對於其他租用，將會針對 B2B 使用者按照標準 SSPR 程序進行，類似於資源租用將被鎖定之情況下 B2B 使用者的成員 SSPR。

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>它也會針對病毒式租用戶中的使用者啟用嗎？
目前不支援。

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM 是否會提供 Azure AD B2B 共同作業的線上支援？
用以提供這項支援的工作正在進行中。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>什麼是新建立的 B2B 共同作業使用者之初始密碼存留期？
Azure AD 有一組固定的字元、密碼強度，以及帳戶鎖定需求，可同樣適用於所有 Azure AD 雲端使用者帳戶。 雲端使用者帳戶是不與另一個身分識別提供者 (例如 Microsoft 帳戶、Facebook、ADFS 或甚至是另一個 (如果是 B2B 共同作業) 的雲端租用戶) 同盟的帳戶。 針對同盟的帳戶，密碼原則取決於內部部署租用和使用者的 Microsoft 帳戶設定中的原則。

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>應用程式希望區分租用戶使用者和來賓使用者的經驗。 有適用於此案例的標準指引嗎？ 存在的識別提供者是否宣告了適用於此案例的正確模型？
 
來賓使用者可以使用任何識別提供者，依我們在 [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)中討論的方式進行驗證。 因此，使用 UserType 是判斷此項目的正確屬性。 UserType 宣告目前不包含在權杖中。 應用程式應該使用圖形 API 來查詢使用者適用的目錄，並取得其 UserType。

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>哪裡可以找到 B2B 共同作業社群，以分享解決方案並提交構想？

我們會時常聆聽您對改進 B2B 共同作業的意見反應。 我們邀請您在 [Microsoft 技術社群 (英文)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) 參與討論、分享您的使用者案例、最佳作法，以及您對 Azure AD B2B 共同作業的意見
 
我們也邀請您前往 [B2B 共同作業構想 (英文)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) 網站提交您的構想和針對未來的功能投票。

### <a name="is-there-a-way-to-invite-the-user-such-that-the-invitation-is-automatically-redeemed-and-the-user-is-just-ready-to-go-or-will-the-user-always-have-to-click-through-to-the-redemption-url"></a>是否有辦法以自動兌換邀請的方式來邀請使用者，讓使用者直接「準備好開始進行」，還是使用者一律必須一路點選至兌換 URL？
如果傳送邀請的邀請組織使用者同時也是受邀組織 (B2B 使用者的組織) 的成員，B2B 使用者就不需要兌換該邀請。

建議您從受邀組織中邀請一位使用者加入邀請組織來達到這個目的。 [將這個使用者新增到資源組織中的來賓邀請者角色](active-directory-b2b-add-guest-to-role.md)。 這個使用者可以透過登入 UI、PowerShell 指令碼或 API 邀請受邀組織中的其他使用者，而不需要求來自該組織的 B2B 使用者兌換其邀請。

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>當受邀的合作夥伴使用同盟來新增自己的內部部署驗證時，B2B 共同作業如何運作？
如果合作夥伴具有與內部部署驗證基礎結構同盟的 Azure AD 租用戶，就會自動達成內部部署單一登入 (SSO)。 如果合作夥伴沒有 Azure AD 租用戶，則系統會為到來的使用者建立 Azure AD 帳戶。 

### <a name="i-didnt-think-azure-ad-b2b-would-accept-gmailcom-and-outlookcom-email-addresses-b2c-was-what-you-used-for-those"></a>我不認為 Azure AD B2B 會接受 gmail.com 和 outlook.com 電子郵件地址。 適用於這些地址的是 B2C。
我們正在消弭 B2B 與 B2C 在所支援身分識別方面的差異。 所使用的身分識別並不是決定使用 B2B 或 B2C 的良好依據。 請參閱下列文章來協助您決定要使用哪一種：[比較 Azure Active Directory 的 B2B 共同作業和 B2C](active-directory-b2b-compare-b2c.md)。

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>哪些應用程式和服務支援 Azure B2B 來賓使用者？
所有 Azure AD 整合式應用程式。 

### <a name="is-it-possible-to-force-mfa-for-b2b-guest-users-if-partners-have-no-mfa-enabled"></a>如果合作夥伴未啟用 MFA，是否可以針對 B2B 來賓使用者強制執行 MFA？
是。 如需詳細資料，請參閱 [B2B 共同作業使用者的條件式存取](active-directory-b2b-mfa-instructions.md)。

### <a name="within-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-any-plans-to-extend-this-to-azure-or-across-all-of-office-365"></a>在 SharePoint 中，您可以針對外部使用者定義「允許」或「拒絕」清單。 是否有任何將此功能延伸到 Azure 或整個 Office 365 的計劃？
是。 Azure AD B2B 共同作業支援允許清單/拒絕清單功能。 

### <a name="what-licenses-are-needed-for-azure-ad-b2b"></a>Azure AD B2B 需要什麼授權？
請參閱 [Azure Active Directory B2B 共同作業授權指引](active-directory-b2b-licensing.md)。

### <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
* [疑難排解 Azure Active Directory B2B 共同作業](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)

