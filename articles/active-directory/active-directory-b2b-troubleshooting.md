---
title: "針對 Azure Active Directory B2B 共同作業問題進行疑難排解 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業常見問題的補救方式"
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
ms.date: 03/14/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 66c0084c89b5c7510196142afd27b58953d0dc86
ms.lasthandoff: 03/15/2017


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>針對 Azure Active Directory B2B 共同作業問題進行疑難排解

以下是 Azure Active Directory (Azure AD) B2B 共同作業常見問題的補救方式。

## <a name="i-cant-create-an-external-user-due-to-an-existing-contact"></a>由於現有的連絡人，我無法建立外部使用者

若您要邀請的外部使用者已經有連絡人物件，在您解決衝突之前 (通常是透過移除該連絡人物件)，您將無法邀請該使用者。 在 B2B 共同作業正式推出之前，您必須以手動方式解決衝突。

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>我已新增外部使用者，但在我的全域通訊錄或人員選擇器中未看到他們

若清單中未顯示外部使用者，該物件可能需要一些時間才能複寫。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 來賓使用者不會顯示在 SharePoint Online/OneDrive 人員選擇器中 
 
在 SharePoint Online 人員選擇器中搜尋現有來賓使用者的功能預設為關閉，以符合舊版的行為。
您可以使用租用戶和網站集合層級中的 'ShowPeoplePickerSuggestionsForGuestUsers' 設定來啟用此功能。 您可以使用 Set-SPOTenant 和 Set-SPOSite Cmdlet 來設定此功能，讓成員搜尋目錄中所有的現有來賓使用者。 租用戶範圍中的變更不會影響已佈建的 SPO 網站。

## <a name="invitations-have-been-disabled-for-directory"></a>已針對目錄停用邀請

若看到錯誤訊息指出您沒有邀請使用者的權限，請確認您的使用者帳戶已獲邀請外部使用者的授權。 這可以在 [使用者設定] 下完成：

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

若您最近曾修改這些設定或已獲指派使用者的「來賓邀請者」角色，變更可能需要 15-60 分鐘才會生效。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>我邀請的使用者在兌換期間遇到錯誤

常見錯誤包括：

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>受邀者的管理員不允許在其租用戶中建立 EmailVerified 使用者

當邀請使用者的組織使用 Azure Active Directory，但指定使用者的帳戶不存在 (例如，使用者不存在於 Azure AD contoso.com 中)。 contoso.com 的系統管理員可能已建立防止建立使用者的原則。 使用者必須向管理員確認，以判斷是否允許外部使用者。 外部使用者的管理員需要允許其網域中存在已驗證的電子郵件使用者 (請參閱[本文](https://docs.microsoft.com/powershell/msonline/v1/set-msolcompanysettings#parameters)，了解允許已驗證的電子郵件使用者)。

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部使用者不存在於同盟網域中

若外部使用者使用同盟解決方案，其中驗證是在內部部署環境中執行且該使用者尚未存在於 Azure Active Directory 中，則無法邀請該使用者。

若要解決此問題，外部使用者的系統管理員必須將該使用者的帳戶同步到 Azure Active Directory。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>‘\#’ (通常是無效的字元) 如何與 Azure AD 同步？

“\#” 是 Azure AD B2B 共同作業中的 UPN 或外部使用者保留字元 (亦即，若邀請 &lt;user@contoso.com&gt;，它會變成 &lt;user_contoso.com#EXT@fabrikam.onmicrosoft.com&gt;)，因此來自內部部署之 UPN 中的 \# 不允許登入 Azure 入口網站。

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>我在將外部使用者新增到已同步的群組時遇到錯誤

您只能將外部使用者新增到「已指派」或「安全性」群組，而無法指派到主控內部部署群組。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>我的外部使用者未收到兌換電子郵件

受邀者應該連絡其 ISP 或檢查其垃圾郵件篩選設定，以確定下列地址在允許清單中：&lt;Invites@microsoft.com&gt;

## <a name="my-recipient-received-multiple-emails-from-me"></a>我的收件者收到來自我的多封電子郵件

在某些邀請收件者的帳戶有多個別名的情況下，他們可能會收到兩個邀請。 在這些情況下，第一個兌換的連結是會建立的帳戶，而第二個兌換連結會失效。

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
* [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
* [B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)
* [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 共同作業常見問題集 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 共同作業 API 和自訂](active-directory-b2b-api.md)
* [適用於 B2B 共同作業使用者的多重要素驗證](active-directory-b2b-mfa-instructions.md)
* [在沒有邀請的情況下新增 B2B 共同作業使用者](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory 中應用程式管理的文章索引](active-directory-apps-index.md)

