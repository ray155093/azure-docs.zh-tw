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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 3e92da61128a18c42726f4c3c85cd6e5024a697f
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Azure Active Directory B2B 共同作業常見問題集 (FAQ)

常見問題集會定期更新以反映新的興趣。

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>此功能是否可在 Azure 傳統入口網站中取得？
此 Azure AD B2B 共同作業公開預覽版本中重新整理中的新功能都只能透過 [Azure 入口網站](https://portal.azure.com)和新的存取面板取得。 試試看！

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B 共同作業的使用者可以存取 SharePoint Online 和 OneDrive 嗎？
您的 B2B 共同作業來賓使用者會在目錄中。 您可以將它們新增到您可以使用權限之 SharePoint Online 和 OneDrive 站台的群組，或甚至直接從 SharePoint Online 的人員選擇器選取它們。 由於這些都是來賓使用者，SharePoint Online 網站必須啟用外部共用。

### <a name="is-the-csv-upload-mechanism-still-supported"></a>是否仍支援 CSV 上傳機制？
是。 請參閱我們已加入的 PowerShell 範例。

### <a name="how-can-i-customize-my-invitation-emails"></a>如何自訂我的邀請電子郵件？
您可以使用 B2B 邀請 API 自訂幾乎所有關於邀請者程序的項目。

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>受邀的外部使用者是否可以離開他已受邀的組織？
這在此公開預覽版重新整理中目前不提供。

### <a name="can-i-use-my-microsoft-account-johncontosomicrosoftacctcom-to-sign-in-to-resources"></a>我可以使用 Microsoft 帳戶 (John@contosomicrosoftacct.com) 登入資源嗎？
在此公開預覽版重新整理期間不可使用您的 Microsoft 帳戶。 如果您使用非標準的 Microsoft 帳戶後置字元 (可能適用於公司郵件，例如 @contoso.com),，Azure Active Directory 租用戶將會建立以供您使用。

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>現在，Multi-Factor Authentication (MFA) 可供來賓使用者使用，他們是否也可以重設其 MFA 方法？
是的，一般使用者可以的相同方式。

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>什麼公司負責 MFA 授權？
邀請組織是介入並執行 MFA 的組織。 因此，邀請組織負責確定它們有足夠的授權可提供給執行 MFA 之 B2B 使用者。

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>如果我的合作夥伴組織已設定 MFA 會如何？ 我們可以信任其 MFA 且不使用我們的 MFA 嗎？
不在此公開預覽版本重新整理中，但我們在未來的版本將會支援這個。 發行時，您將能夠選取要從您的 (邀請組織的) MFA 排除之特定協力廠商。

### <a name="how-can-i-achieve-delayed-invitations"></a>如何達成延遲的邀請？
有些組織想要新增 B2B 共同作業使用者、將它們佈建至需要佈建的應用程式，然後送出邀請。 如果您是，您可以使用 B2B 共同作業邀請 API 自訂登入工作流程。

### <a name="can-guest-users-and-contacts-co-exist"></a>來賓使用者和連絡人是否可以同時存在？
您的組織可能已經新增代表外部共同作業者的連絡人，因此它們會在全域通訊清單中，以及電子郵件撰寫期間之電子郵件地址建議中顯示。 您可能會想知道您現在將這些相同的共同作業者新增為目錄中的 B2B 共同作業使用者時會發生什麼事，對嗎？ 在未來版本中，B2B 共同作業使用者和您的連絡人物件都可以同時存在您公司的目錄中。 敬請期待我們的通知！

### <a name="can-i-make-my-guest-users-limited-admins"></a>我是否可以為來賓使用者提供有限的系統管理員權限？
當然。 如果這是您的組織所需，請在[將來賓使用者新增至角色](active-directory-users-assign-role-azure-portal.md)中了解如何進行。

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B 共同作業是否支援允許 B2B 使用者存取 Azure 入口網站？
B2B 共同作業的使用者應該不需要存取 Azure 入口網站，除非他們獲指派有限的系統管理員或全域管理員角色。 在此情況下，他們可以存取入口網站。 如果不是這些角色中的來賓使用者存取入口網站，則他/她可能無法存取經驗的某些部分，因為來賓使用者角色在目錄中擁有特定權限，如先前幾節中所述。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>我可以封鎖來賓使用者存取 Azure 入口網站嗎？
可以！ 但是當您設定此原則以避免不小心封鎖存取成員和系統管理員時要小心。
您可以執行下列三個步驟，透過 Windows Azure 服務管理 API 的條件式存取原則封鎖來賓使用者存取 [Azure 入口網站](https://portal.azure.com)。
1. 修改**所有使用者**群組，以僅包含特定成員![](media/active-directory-b2b-faq/modify-all-users-group.png)
2. 建立包含來賓使用者的動態群組![](media/active-directory-b2b-faq/group-with-guest-users.png)
3. 設定條件式存取原則以防止來賓使用者存取入口網站，如下列影片中所示。

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player]

  如果沒有內嵌顯示這段影片，您可以在[這裡](https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user)取得。

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Azure AD B2B 共同作業會根據哪一個時間表啟動 MFA 和取用者電子郵件帳戶的支援？
現在此公開預覽版本重新整理支援 MFA 和取用者電子郵件帳戶。

### <a name="what-is-the-ga-timeline-for-azure-ad-b2b"></a>什麼是 Azure AD B2B 的 GA 時間表？
我們執行這項操作的時機取決於目前功能集接收來自客戶的意見反應。

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>是否有支援 Azure AD B2B 共同作業使用者密碼重設的計劃？
是的，這兩種都支援 B2B 共同作業 (來賓) 使用者。

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>它也會針對病毒式租用戶中的使用者啟用嗎？
目前不支援。

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM 是否會提供 Azure AD B2B 共同作業的線上支援？
CRM 公開上市之後，會提供 Azure AD B2B 共同作業的支援。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>什麼是新建立的 B2B 共同作業使用者之初始密碼存留期？
Azure AD 有一組固定的字元、密碼強度，以及帳戶鎖定需求，可同樣適用於所有 Azure AD 雲端使用者帳戶。 雲端使用者帳戶是不與另一個身分識別提供者 (例如 Microsoft 帳戶、Facebook、ADFS 或甚至是另一個 (如果是 B2B 共同作業) 的雲端租用戶) 同盟的帳戶。 針對同盟的帳戶，密碼原則取決於內部部署租用和使用者的 Microsoft 帳戶設定中的原則。

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

