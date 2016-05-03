<properties
	pageTitle="Azure AD 網域服務︰啟用密碼同步處理 | Microsoft Azure"
	description="開始使用 Azure Active Directory 網域服務"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="maheshu"/>

# Azure AD 網域服務 (預覽) - 啟用 Azure AD 網域服務的密碼同步處理

## 工作 5︰為僅限雲端的 Azure AD 目錄啟用 AAD 網域服務的密碼同步處理
一旦您已針對 Azure AD 租用戶啟用 Azure AD 網域服務之後，下一個工作就是讓認證同步處理至 Azure AD 網域服務。這讓使用者能夠使用他們的公司認證來登入受管理的網域。

所涉及的步驟會根據組織擁有僅限雲端的 Azure AD 目錄，或已設定為使用 Azure AD Connect 來與內部部署目錄同步處理而有所不同。

<br>

> [AZURE.SELECTOR]
- [僅限雲端的 Azure AD 目錄](active-directory-ds-getting-started-password-sync.md)
- [同步處理的 Azure AD 目錄](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### 為僅限雲端的 Azure AD 目錄啟用 NTLM 和 Kerberos 認證雜湊產生功能
如果您的組織擁有僅限雲端的 Azure AD 目錄，需要使用 Azure AD 網域服務的使用者就必須變更其密碼。此密碼變更程序會在 Azure AD 中產生 Azure AD 網域服務進行 Kerberos 和 NTLM 驗證所需的認證雜湊。您可以針對租用戶中所有需要使用 Azure AD 網域服務的使用者來使其密碼過期，或者指示這些使用者變更他們的密碼。

以下是您需要提供使用者才能變更其密碼的指示：

1. 瀏覽至組織的 [Azure AD 存取面板] 頁面。您通常可以在 [http://myapps.microsoft.com](http://myapps.microsoft.com) 上找到此頁面。

2. 選取此頁面上的 [設定檔] 索引標籤。

3. 按一下此頁面上的 [變更密碼] 圖格來初始密碼變更。

    ![建立適用於 Azure AD 網域服務的虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. 這會顯示 [變更密碼] 頁面。使用者可以輸入現有的 (舊) 密碼，然後繼續變更密碼。

    ![建立適用於 Azure AD 網域服務的虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

當使用者變更密碼之後，新的密碼短時間內就能在 Azure AD 網域服務中使用。幾分鐘之後，使用者就能使用最近變更的密碼，來登入已加入受管理網域的電腦。


<br>

## 相關內容

- [為同步處理的 Azure AD 目錄啟用 AAD 網域服務的密碼同步處理](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administer an Azure AD Domain Services managed domain (管理 Azure AD 網域服務受管理的網域)](active-directory-ds-admin-guide-administer-domain.md)

- [將 Windows 虛擬機器加入 Azure AD 網域服務受管理的網域](active-directory-ds-admin-guide-join-windows-vm.md)

- [將 Red Hat Enterprise Linux 虛擬機器加入 Azure AD 網域服務受管理的網域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0427_2016-->