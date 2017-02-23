---
title: "為 Azure Active Directory 中的 B2B 共同作業設定 SaaS 應用程式 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業的程式碼與 PowerShell 範例"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>為 B2B 共同作業設定 SaaS 應用程式

Azure Active Directory (Azure AD) B2B 共同作業可搭配已與 Azure AD 整合的大部分應用程式使用。 在本節中，我們將逐步說明如何設定一些常見 SAS 應用程式以搭配 Azure AD B2B 使用。
在我們查看應用程式特定指示之前，以下是一些主要規則：

* 請記住，對於大部分的應用程式，必須手動執行使用者佈建 (亦即，必須一併在應用程式中手動建立使用者)。

* 對於支援自動佈建的應用程式 (例如 Dropbox)，邀請是另行從應用程式建立。 使用者務必接受每個邀請。

* 在 [使用者屬性] 中，一律將 [使用者識別碼] 設定為 user.mail (以減少與來賓使用者中錯誤之 UPD 相關的任何問題)


##<a name="dropbox-for-business"></a>DropBox for Business

對於能使用其組織帳戶登入的使用者，您必須手動設定 Dropbox for Business，才能使用 Azure AD 做為 SAML 身分識別提供者。 Dropbox for Business 無法顯示提示或允許使用者使用 Azure AD 來登入 (若尚未這樣設定)。

1. 新增 DropBox for Business 應用程式到 Azure AD，如螢幕擷取畫面中所示。

  ![新增 Dropbox 到 Azure AD](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![新增 Dropbox 到 Azure AD](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. 設定應用程式。

  ![為應用程式設定單一登入](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. 選取單一登入設定並將使用者識別碼變更為 user.mail (預設值是其 UPN)

4. 下載要為 DropBox 設定使用的憑證。

  ![下載憑證](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. 使用 [設定DropBox] 選項 (下面的螢幕擷取畫面將會詳細說明此程序)

6. 取得將在設定中使用的 SAML 單一登入 URL。

7. 從 [DropBox 設定] 頁面取得登入 URL。

  ![登入 Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![Dropbox 功能表](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![Dropbox 驗證對話方塊已摺疊](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![Dropbox 驗證對話方塊已展開](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. 在這裡上傳憑證並貼上 SAML 單一登入 URL。

  ![貼上 SAML SSO URL](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. 在 Azure 入口網站設定自動使用者佈建。

  ![設定自動使用者佈建](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. 一旦在 DropBox 應用程式中佈建之後，來賓/成員使用者將從 DropBox 取得獨立的邀請。 受邀者必須按一下連結以接受它，以在 DropBox 中使用單一登入。

## <a name="box"></a>Box
本節概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Box 中驗證來賓使用者。 在此程序中，您必須將中繼資料上傳至 Box.com。

1. 從企業應用程式新增 Box

2. 設定單一登入

  ![設定 Box 單一登入](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. 首先，確定已在 Azure 管理入口網站正確針對 Box 設定登入 URL。 這是您的 Box.com 租用戶 URL，而且格式如下：https://.box.com。

4. 請注意，「識別碼」不適用於此應用程式，但仍會顯示為必要欄位。

5. user.mail (以確定來賓帳戶的 SSO) 的使用者識別碼

6. 建立新的 SAML 憑證

7. 若要開始設定您的 Box.com 租用戶以使用 Azure Active Directory 做為身分識別提供者，請下載下列中繼資料檔案，並將它儲存到您電腦本機：下載中繼資料檔案 (請務必將它啟用)

8. 將該中繼資料檔案轉寄給 Box 支援小組。 支援小組會為您設定單一登入。

9. 針對 Azure AD 自動使用者佈建進行設定。

  ![授權 Azure AD 以連線到 Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

受邀者也必須從 Box 應用程式兌換其邀請。

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2B 共同作業邀請](active-directory-b2b-delegate-invitations.md)
* [動態群組與 B2B 共同作業](active-directory-b2b-dynamic-groups.md)
* [B2B 共同作業程式碼與 PowerShell 範例](active-directory-b2b-code-samples.md)
* [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業使用者宣告對應](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


