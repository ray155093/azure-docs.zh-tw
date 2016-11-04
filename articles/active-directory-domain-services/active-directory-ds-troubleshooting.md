---
title: Azure Active Directory Domain Services：疑難排解指南 | Microsoft Docs
description: Azure AD 網域服務的疑難排解指南
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: maheshu

---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD 網域服務 - 疑難排解指南
這篇文章提供設定或管理 Azure Active Directory (AD) 網域服務時，可能會遇到的問題之疑難排解提示。

### <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>您無法為 Azure AD 目錄啟用 Azure AD 網域服務
如果您嘗試為您的目錄啟用 Azure AD 網域服務時，遇到失敗或切換回 [已停用] 的情況，請執行下列疑難排解步驟：

* 請確定現有網域的名稱並未與該虛擬網路上可用的網域名稱相同。 例如，假設您有名為 'contoso.com' 的網域已可用於選取的虛擬網路。 接著，您可嘗試在該虛擬網路上啟用具有相同網域名稱 (即 'contoso.com') 的 Azure AD 網域服務受管理網域。 您在嘗試啟用 Azure AD 網域服務時發生錯誤。 這個錯誤是因為名稱與該虛擬網路上的網域名稱衝突。 在此情況下，您必須使用不同的名稱來設定 Azure AD 網域服務受管理網域。 或者，您可以解除佈建現有的網域，然後繼續啟用 Azure AD 網域服務。
* 查看 Azure AD 目錄中是否有名稱為 [Azure AD 網域服務同步處理] 的應用程式。 如果此應用程式存在，您必須將它刪除，然後再重新啟用 Azure AD 網域服務。 執行下列步驟，以檢查應用程式是否存在並將它刪除 (如果應用程式存在的話)：
  
  1. 瀏覽到 **Azure 傳統入口網站** ([https://manage.windowsazure.com](https://manage.windowsazure.com))。
  2. 在左窗格中，選取 [Active Directory]  節點。
  3. 選取您要啟用 Azure AD 網域服務的 Azure AD 租用戶 (目錄)。
  4. 瀏覽至 [應用程式]  索引標籤。
  5. 選取下拉式清單中的 [我公司所擁有的應用程式]  選項。
  6. 檢查名為 [Azure AD 網域服務同步處理] 的應用程式。 如果應用程式存在，請繼續將它刪除。
  7. 刪除此應用程式後，請嘗試再次啟用 Azure AD 網域服務。

### <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>使用者無法登入 Azure AD 網域服務管理的網域
如果 Azure AD 租用戶中有一或多個使用者無法登入新建立的受管理網域，請執行下列疑難排解步驟：

* 嘗試使用 UPN 格式 (例如，'joeuser@contoso.com')) 登入，而非使用 SAMAccountName 格式 ('CONTOSO\joeuser')。 有時候，對於 UPN 前置詞過長或與受管理網域上其他使用者相同的使用者，可能會自動產生 SAMAccountName。 UPN 格式保證是 Azure AD 租用戶內唯一的格式。

> [!NOTE]
> 建議使用 UPN 格式來登入 Azure AD 網域服務受管理網域。
> 
> 

* 確定您已根據《入門指南》中所述的步驟來 [啟用密碼同步處理](active-directory-ds-getting-started-password-sync.md) 。
* **外部帳戶** 確定受影響的使用者帳戶不是 Azure AD 租用戶中的外部帳戶。 外部帳戶的範例包括 Microsoft 帳戶 (例如 'joe@live.com')) 或來自外部 Azure AD 目錄的使用者帳戶。 因為 Azure AD 網域服務沒有這類使用者帳戶的認證，這些使用者會無法登入受管理的網域。
* **同步處理的帳戶：** 如果受影響的使用者帳戶會從內部部署目錄同步處理，請確認：
  
  * 您已部署或更新為 [Azure AD Connect 的最新版本](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect)。
  * 您已設定 Azure AD Connect 以 [執行完整同步處理](active-directory-ds-getting-started-password-sync.md)。
  * 根據您的目錄大小，可能需要一些時間，使用者帳戶和認證雜湊才可在 Azure AD 網域服務中提供使用。 確定您已等候足夠長的時間之後，再重試驗證 (視目錄大小的不同，可能需要幾個小時到一天的時間，大型目錄則可能需要兩天)。
  * 如果在您驗證上述步驟之後問題仍持續發生，請嘗試重新啟動 Microsoft Azure AD 同步服務。 在您的同步電腦上，啟動命令提示字元，然後執行下列命令：
    
    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **僅限雲端帳戶**：如果受影響的使用者帳戶是僅限雲端的使用者帳戶，請確定在您啟用 Azure AD 網域服務之後，使用者已變更其密碼。 這個步驟會導致產生 Azure AD 網域服務所需的認證雜湊。

### <a name="contact-us"></a>與我們連絡
請連絡 Azure Active Directory Domain Services 產品小組，[分享意見或尋求支援](active-directory-ds-contact-us.md)。

<!--HONumber=Oct16_HO2-->


