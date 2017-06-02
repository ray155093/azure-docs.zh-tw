---
title: "Azure Active Directory B2C：常見問題集 | Microsoft Docs"
description: "關於 Azure Active Directory B2C 的常見問題集"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 1b62ab8fe4f8a736821eb9da3d77fe2f654de745
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017


---
# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C：常見問題集
此頁面會回答有關 Azure Active Directory (Azure AD) B2C 的常見問題。 請隨時回來查看最新消息。

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>我可以在以員工為主的現有 Azure AD 租用戶中使用 Azure AD B2C 功能嗎？
目前您無法在現有的 Azure AD 租用戶中開啟 Azure AD B2C 功能。 建議您建立另一個租用戶，使用 Azure AD B2C 功能來管理您的取用者。

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>我可以使用 Azure AD B2C 來提供 Office 365 的社交登入 (Facebook 和 Google+) 嗎？
Azure AD B2C 無法與 Microsoft Office 365 一起使用。 一般而言，它不能用來提供任何對 SaaS app (Office 365、Salesforce、Workday 等) 的驗證。 它只會為取用者導向的 Web 和行動應用程式提供身分識別與存取管理，並不適用於員工或合作夥伴的案例。

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C 中的本機帳戶是什麼？ 與 Azure AD 中的工作或學校帳戶有何不同？
在 Azure AD 租用戶中，租用戶的每位使用者 (不包括目前有 Microsoft 帳戶的使用者) 都是以 `<xyz>@<tenant domain>` 格式的電子郵件地址登入，當中的 `<tenant domain>` 是租用戶已驗證的其中一個網域，或初始 `<...>.onmicrosoft.com` 網域。 這種類型的帳戶就是工作或學校帳戶。

在 Azure AD B2C 租用戶中，大部分的應用程式都希望使用者以任意的電子郵件地址登入 (例如 joe@comcast.net、bob@gmail.com、sarah@contoso.com 或 jim@live.com。 這種類型的帳戶就是本機帳戶。 現在，我們也支援任意的使用者名稱 (僅單純字串) 做為本機帳戶 (例如，joe、bob、sarah 或 jim)。 您可以在 Azure AD B2C 服務中選擇這兩個本機帳戶類型的其中之一。

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>你們現在支援哪些社交身分識別提供者？ 你們打算在未來支援哪些？
我們目前支援 Facebook、Google+、LinkedIn 和 Amazon。 根據客戶需求，我們將會增加支援其他熱門的社交身分識別提供者。

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>我可以設定範圍，以便從各種社交身分識別提供者收集取用者的詳細資訊嗎？
否，但這項功能已在我們的規劃中。 我們支援的一組社交身分識別提供者所使用的預設範圍如下：

* Facebook: email
* Google+: email
* Microsoft 帳戶︰openid 電子郵件設定檔
* Amazon: profile
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>我的應用程式必須在 Azure 上執行，才能使用 Azure AD B2C 嗎？
否，您可以將應用程式裝載於任何地方 (雲端或內部部署)。 只要能夠在公開存取的端點上傳送和接收 HTTP 要求，就可以與 Azure AD B2C 互動。

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>我有多個 Azure AD B2C 租用戶。 如何在 Azure 入口網站上管理它們？
在 Azure 入口網站上，每一個 Azure AD B2C 租用戶都有自己的 B2C 功能刀鋒視窗。 請參閱 [Azure Active Directory B2C：註冊您的應用程式](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) ，以了解您如何在 Azure 入口網站上瀏覽至特定租用戶的 B2C 功能刀鋒視窗。 在大部分的瀏覽器中，當您在 Azure 入口網站的 Azure AD B2C 目錄之間切換時，B2C 功能刀鋒視窗不會持續開啟。

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>我如何自訂 Azure AD B2C 傳送的驗證電子郵件 (內容和 [寄件者:] 欄位)？
您可以使用 [公司商標功能](../active-directory/active-directory-add-company-branding.md) 自訂驗證電子郵件的內容。 明確地說，您可以自訂電子郵件的下列兩個元素：

* **橫幅標幟**：顯示在右下方。
* **背景色彩**：顯示在頂端。
  
    ![自訂驗證電子郵件的螢幕擷取畫面](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

電子郵件簽章包含您第一次建立 B2C 租用戶時提供的 B2C 租用戶名稱。 您可以使用這些指示變更名稱：

* 以「訂用帳戶管理員」身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com/) 。
* 瀏覽至您的 B2C 租用戶。
* 按一下 [設定]  索引標籤。
* 變更 [目錄屬性] 區段底下的 [名稱] 欄位。
* 按一下頁面底部的 [儲存]  。

目前無法變更電子郵件中的 [從:] 欄位。 如果您對此功能和完整自訂驗證電子郵件內文有興趣，請投票支持 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails)上建議的功能。

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>我如何將現有的使用者名稱、密碼和設定檔從資料庫移轉至 Azure AD B2C？
您可以使用 Azure AD 圖形 API 來撰寫您的移轉工具。 如需詳細資料，請參閱 [圖形 API 範例](active-directory-b2c-devquickstarts-graph-dotnet.md) 。 未來我們將提供各種現成的移轉選項和工具。

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C 中用於本機帳戶的密碼原則為何？
Azure AD B2C 的本機帳戶密碼原則是以 Azure AD 的原則為基礎。 Azure AD B2C 的註冊、註冊或登入和密碼重設原則會使用「強式」密碼強度，而且不會讓任何密碼到期。 如需更詳細的資料，請閱讀 [Azure AD 中的密碼原則](https://msdn.microsoft.com/library/azure/jj943764.aspx) 。

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>我可以使用 Azure AD Connect，將儲存於內部部署 Active Directory 的取用者身分識別移轉至 Azure AD B2C 嗎？
否，Azure AD Connect 不是設計來搭配 Azure AD B2C 一起使用。 未來我們將提供各種現成的移轉選項和工具。

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>我的應用程式是否能在 iFrame 內開啟 Azure AD B2C 頁面？
否，基於安全性考量，無法在 iFrame 內開啟 Azure AD B2C 頁面。  我們的服務會與瀏覽器通訊以禁止此行為。  安全性社群整體和 OAUTH2 規格的建議是不要使用 iframe 來提供身分識別體驗，因為會有點擊劫持風險。

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C 可以搭配 Microsoft Dynamics 之類的 CRM 系統一起使用嗎？
目前不支援。 整合這些系統已在我們的規劃中。

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C 可以搭配 SharePoint 內部部署的 2016 或更舊版本一起使用嗎？
目前不支援。 Azure AD B2C 不支援入口網站和電子商務應用程式建置在 SharePoint 內部部署需要的 SAML 1.1 權杖。 請注意，Azure AD B2C 不適用於 SharePoint 外部夥伴共用的案例。請改以參閱 [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx)。

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>我應該使用 Azure AD B2C 或 B2B 來管理外部身分識別？
請閱讀這篇關於 [外部身分識別](../active-directory/active-directory-b2b-compare-external-identities.md) 的文章，以深入了解將適當的功能套用至外部身分識別的案例。

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C 提供哪些報告和稽核功能？ 它們與 Azure AD Premium 的功能相同嗎？
否，Azure AD B2C 不支援與 Azure AD Premium 相同的一組報告。 不過它們有許多共同點。  
* 登入報表會記錄每次登入並提供經過縮減的詳細資料。  
* 稽核報告可在 Azure 入口網站中取得，位置在 Azure Active Directory > 活動-稽核記錄 > 選擇 B2C 並視需要套用篩選。 管理活動以及應用程式活動都包括在內。 
* 您可以在[使用報告 API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-usage-reporting-api)中取得使用報告，裡面記載了使用者人數、登入次數及 MFA 數量

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>我可以將 Azure AD B2C 所提供的頁面 UI 當地語系化嗎？ 支援哪些語言？
目前，Azure AD B2C 僅針對英文做過最佳化。 我們計劃儘速推出當地語系化功能。

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>我可以在 Azure AD B2C 提供的註冊與登入頁面上使用自己的 URL 嗎？ 例如，我可以將 URL 從 login.microsoftonline.com 變更為 login.contoso.com 嗎？
目前不支援。 但這項功能已在我們的規劃中。 另請注意，在 Azure 傳統入口網站上租用戶的 [網域]  索引標籤中驗證您的網域，將不會執行此動作。

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>如何刪除 Azure AD B2C 租用戶？
請遵循下列步驟來刪除 Azure AD B2C 租用戶︰

* 遵循下列步驟以 [瀏覽至 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) (位於 Azure 入口網站上)。
* 瀏覽至 [應用程式]、[識別提供者] 和 [所有原則] 刀鋒視窗，並刪除每個地方的所有項目。
* 現在，以訂用帳戶管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com/) 。 (此為您註冊 Azure 時所用的相同工作或學校帳戶，或是相同的 Microsoft 帳戶)。
* 瀏覽至左側的 Active Directory 擴充，然後按一下 B2C 租用戶。
* 按一下 [使用者]  索引標籤。
* 逐一選取每個使用者 (不包括您目前登入的使用者身分，亦即，訂用帳戶管理員)。 按一下頁面底部的 [刪除]，然後在出現提示時按一下 [是]。
* 按一下 [ **應用程式** ] 索引標籤。
* 選取 [顯示] 下拉式欄位中的 [我公司所擁有的應用程式]，然後按一下核取記號。
* 您會看到下面列出名稱為 **b2c-extensions-app** 的應用程式。 按一下頁面底部的 [刪除]，然後在出現提示時按一下 [是]。
* 再次瀏覽至 Active Directory 擴充，然後選取 B2C 租用戶。
* 按一下頁面底部的 [ **刪除** ]。 依照畫面上的指示完成程序。

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>我可以從 Enterprise Mobility Suite 中取得 Azure AD B2C 嗎？
否，Azure AD B2C 是隨用隨付的 Azure 服務，並不是 Enterprise Mobility Suite 的一部分。

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>如何報告有關 Azure AD B2C 的問題？
請參閱 [提出 Azure Active Directory B2C 的支援要求](active-directory-b2c-support.md)。



