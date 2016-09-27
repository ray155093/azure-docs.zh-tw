<properties 
	pageTitle="開始在雲端中使用 Microsoft Azure Multi-Factor Authentication"
	description="這是說明如何開始在雲端中使用 Azure MFA 的 Microsoft Azure Multi-Factor Authentication 頁面。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="kgremban"/>

# 開始在雲端中使用 Azure Multi-Factor Authentication
在下列文章中，您將學習如何在雲端中開始使用 Azure Multi-Factor Authentication。

> [AZURE.NOTE]  以下文件提供有關如何使用 **Azure 傳統入口網站**啟用使用者的詳細資訊。如果您要尋找有關如何設定適用於 O365 使用者的 Azure Multi-Factor Authentication 的詳細資訊，請參閱[設定適用於 Office 365 的 Multi-Factor Authentication](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=zh-TW&rs=zh-TW&ad=US)。

![雲端中的 MFA](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## 必要條件
需要下列必要條件，您才可以為您的使用者啟用 Azure Multi-Factor Authentication。




- [註冊 Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/) - 如果您還沒有 Azure 訂用帳戶，可以免費註冊。如果您剛開始使用 Azure MFA，您可以使用試用版訂用帳戶
2. [建立 Multi-Factor Auth Provider](multi-factor-authentication-get-started-auth-provider.md) 並將它指派給目錄，或[將授權指派給使用者](multi-factor-authentication-get-started-assign-licenses.md)。

> [AZURE.NOTE]  授權的適用對象是擁有 Azure MFA、Azure AD Premium 或 Enterprise Mobility Suite (EMS) 的使用者。MFA 包含在 Azure AD Premium 和 EMS。如果您有足夠的授權，則不需要建立 Auth Provider。


## 為使用者開啟 Multi-Factor Authentication
若要為使用者開啟 Multi-Factor Authentication，您只要將使用者的狀態從停用變更為啟用。如需使用者狀態的詳細資訊，請參閱 [Azure Multi-Factor Authentication 中的使用者狀態](multi-factor-authentication-get-started-user-states.md)。

您可以使用下列程序為使用者啟用 MFA。

### 開啟 Multi-Factor Authentication
--------------------------------------------------------------------------------
1.  以系統管理員身分登入 **Azure 傳統入口網站**。
2.  在左側按一下 [Active Directory]。
3.  在 [目錄] 下方，針對要啟用的使用者按一下目錄。![按一下目錄](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  在頂端按一下 [使用者]。
5.  在頁面底部，按一下 [管理 Multi-Factor Auth]。![按一下目錄](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  這會開啟新的瀏覽器索引標籤。找出要啟用 Multi-Factor Authentication 的使用者。您可能需要在頂端變更檢視。請確定狀態為 [停用]。![啟用使用者](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  請在其名稱旁的方塊**打勾**。
7.  在右側按一下 [啟用]。![啟用使用者](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  按一下 [啟用 Multi-Factor Auth]。![啟用使用者](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  您應該注意到使用者的狀態已經從 [停用] 變更為 [啟用]。![啟用使用者](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  在您啟用您的使用者後，建議您透過電子郵件通知他們。電子郵件也應該通知他們如何使用其非瀏覽器應用程式，以避免遭到鎖定。


## 使用 PowerShell 自動開啟 Multi-Factor Authentication

若要使用 [Azure AD PowerShell](../powershell-install-configure.md) 變更[狀態](multi-factor-authentication-whats-next.md)，您可以使用下列程式碼。您可以將 `$st.State` 變更為下列其中一個狀態：


- 已啟用
- 已強制
- 已停用

> [AZURE.IMPORTANT]  請注意，如果您直接從 [停用] 狀態進入 [強制] 狀態，非現代化驗證用戶端將會停止運作，因為使用者未通過 MFA 註冊並取得[應用程式密碼](multi-factor-authentication-whats-next.md#app-passwords)。如果您具有非現代化驗證用戶端，而且需要應用程式密碼，則建議您從 [停用] 狀態變更為 [啟用] 狀態。這可讓使用者註冊並取得其應用程式密碼。

		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

使用 PowerShell 是大量啟用使用者的選項。目前在 Azure 入口網站中沒有大量啟用功能，您必須個別選取每個使用者。如果您有許多使用者，這是相當繁重的工作。藉由使用上述程式碼建立 PowerShell 指令碼，您可以循環使用者清單並且啟用他們。下列是一個範例：

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
    	$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
    	$st.RelyingParty = "*"
    	$st.State = “Enabled”
    	$sta = @($st)
    	Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


如需使用者狀態的詳細資訊，請參閱 [Azure Multi-Factor Authentication 中的使用者狀態](multi-factor-authentication-get-started-user-states.md)。

## 後續步驟
現在您已在雲端中設定 Multi-Factor Authentication，接下來您可以設定及安裝您的部署。請參閱 [設定 Azure Multi-Factor Authentication]。

<!---HONumber=AcomDC_0921_2016-->