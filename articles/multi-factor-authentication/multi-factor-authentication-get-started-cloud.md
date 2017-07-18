---
title: "在雲端中開始使用 Azure MFA | Microsoft Docs"
description: "這是說明如何開始在雲端中使用 Azure MFA 的 Microsoft Azure Multi-Factor Authentication 頁面。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 554931e96e073ec2f2f68df2297e1ee21f5eda87
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017

---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>開始在雲端中使用 Azure Multi-Factor Authentication
本文將帶領您逐步了解如何在雲端中開始使用 Azure Multi-Factor Authentication。

> [!NOTE]
> 以下文件提供有關如何使用 **Azure 傳統入口網站**啟用使用者的詳細資訊。 如果您要尋找有關如何設定適用於 O365 使用者的 Azure Multi-Factor Authentication 的詳細資訊，請參閱[設定適用於 Office 365 的 Multi-Factor Authentication](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)。

![雲端中的 MFA](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>必要條件
[註冊 Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/) - 如果您還沒有 Azure 訂用帳戶，可以免費註冊。 如果您剛開始使用 Azure MFA，您可以使用試用版訂用帳戶

## <a name="enable-azure-multi-factor-authentication"></a>啟用 Azure Multi-Factor Authentication
只要您的使用者具有包含 Azure Multi-Factor Authentication 的授權，您就不需要手動開啟 Azure MFA。 您可以開始要求對個別使用者進行雙步驟驗證。 可啟用 Azure MFA 的授權如下︰
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

如果您沒有上述其中一種授權，或沒有足夠的授權來涵蓋所有的使用者，也沒關係。 您只必須執行額外一個步驟以及在您的目錄中[建立多因素驗證提供者](multi-factor-authentication-get-started-auth-provider.md)。

## <a name="turn-on-two-step-verification-for-users"></a>對使用者開啟雙步驟驗證
若要開始對使用者要求雙步驟驗證，請將使用者的狀態從停用變更為啟用。  如需使用者狀態的詳細資訊，請參閱 [Azure Multi-Factor Authentication 中的使用者狀態](multi-factor-authentication-get-started-user-states.md)

您可以使用下列程序為使用者啟用 MFA。

### <a name="to-turn-on-multi-factor-authentication"></a>開啟 Multi-Factor Authentication
1. 以系統管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在左側按一下 [Active Directory] 。
3. 在 [目錄] 下方，針對要啟用的使用者選取目錄。
   ![按一下目錄](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. 在頂端按一下 [使用者] 。
5. 在頁面底部，按一下 [管理 Multi-Factor Auth] 。 隨即開啟新的瀏覽器索引標籤。
   ![按一下目錄](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. 找出要啟用雙步驟驗證的使用者。 您可能需要在頂端變更檢視。 請確定狀態為 **停用。**
   ![啟用使用者](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. 請在其名稱旁的方塊 **打勾** 。
8. 在右側按一下 [啟用] 。
   ![啟用使用者](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. 按一下 [啟用 Multi-Factor Auth] 。
   ![啟用使用者](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. 請注意，使用者的狀態已經從 [停用] 變更為 [啟用]。
    ![啟用使用者](./media/multi-factor-authentication-get-started-cloud/user.png)

在您啟用您的使用者後，請透過電子郵件通知他們。 下次他們嘗試登入時，系統會要求他們針對雙步驟驗證註冊帳戶。 開始使用雙步驟驗證後，他們還需要設定應用程式密碼，以避免遭到鎖定而無法使用非瀏覽器應用程式。

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>使用 PowerShell 自動開啟雙步驟驗證
若要使用 [Azure AD PowerShell](/powershell/azure/overview) 變更[狀態](multi-factor-authentication-whats-next.md)，您可以使用下列程式碼。  您可以將 `$st.State` 變更為下列其中一個狀態：

* 已啟用
* 已強制
* 已停用  

> [!IMPORTANT]
> 我們不鼓勵您直接將使用者從停用狀態改為強制狀態。 非瀏覽器型應用程式會停止運作，因為使用者未通過 MFA 註冊並取得[應用程式密碼](multi-factor-authentication-whats-next.md#app-passwords)。 如果您具有非瀏覽器型應用程式，而且需要應用程式密碼，建議您將狀態從停用改為啟用。 這可讓使用者註冊並取得其應用程式密碼。 之後，您可以將其狀態改為強制。

使用 PowerShell 是大量啟用使用者的選項。 目前在 Azure 入口網站中沒有大量啟用功能，您必須個別選取每個使用者。 如果您有許多使用者，這是相當繁重的工作。 藉由使用下列程式碼建立 PowerShell 指令碼，您可以循環使用者清單並且啟用他們。

```PowerShell

$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)
Set-MsolUser -UserPrincipalName "bsimon@contoso.com" -StrongAuthenticationRequirements $sta
```

下列是一個範例：

```Powershell
$users = @("bsimon@contoso.com", "jsmith@contoso.com", "ljacobson@contoso.com")
foreach ($user in $users)
{
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
}
```

如需詳細資訊，請參閱 [Azure Multi-Factor Authentication 中的使用者狀態](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>後續步驟
現在您已在雲端中設定 Azure Multi-Factor Authentication，接下來您可以設定及安裝您的部署。 如需詳細資訊，請參閱[設定 Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)。


