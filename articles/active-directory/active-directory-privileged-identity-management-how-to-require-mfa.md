<properties
   pageTitle="如何要求 Multi-Factor Authentication | Microsoft Azure"
   description="了解如何利用 Azure Active Directory Privileged Identity Management 擴充功能，為特殊權限的身分識別強制啟用 Multi-Factor Authentication (MFA)。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management：如何要求 MFA

建議您為所有的系統管理員要求 Multi-Factor Authentication (MFA)，而且所有目前和候選的系統管理員都註冊 MFA。這可降低密碼遭入侵所導致的攻擊風險。

您可以要求使用者在登入時完成 MFA 挑戰。部落格文章 [MFA for Office 365 and MFA for Azure (MFA for Office 365 和 MFA for Azure)](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) 比較 Office 和 Azure 訂用帳戶所包含的內容，以及 Microsoft Azure Multi-Factor Authentication 優惠提供的功能。

您也可以要求使用者在啟用 Azure AD PIM 中的角色時，完成 MFA 挑戰。如此一來，如果使用者沒有在登入時完成 MFA 挑戰，PIM 就會提示他們執行這項作業。

## 在 Azure AD Privileged Identity Management 中強制啟用 MFA

當您以安全性系統管理員的身分管理 PIM 中的身分識別時，您可能會看到建議特殊權限帳戶使用 MFA 的警示。按一下 PIM 儀表板中的安全性警示，新的刀鋒視窗會隨即開啟，並顯示應啟用 MFA 的系統管理員帳戶清單。您可以選取多個角色，然後按一下 [修正] 按鈕，以強制啟用 MFA；或者您也可以按一下個別角色旁邊的省略符號，然後按一下 [修正] 按鈕。

> [AZURE.IMPORTANT] 目前不支援使用 Microsoft 帳戶 (例如，@outlook.com、@live.com 或 @hotmail.com) 註冊以用於 Azure MFA，因為系統不允許將它們做為具高度權限的角色的暫時系統管理員。如果使用者需要繼續使用 Microsoft 帳戶來管理工作負載，請立即將他們轉換為永久系統管理員。

或者，您也可以在 PIM 儀表板的 [角色] 區段中按一下特定角色，變更它的 MFA 需求。然後，按一下角色刀鋒視窗中的 [設定]，然後選取 Multi-Factor Authentication 下的 [啟用]。

## Azure AD PIM 如何驗證 MFA

在使用者啟用角色時有兩個選項可用來驗證 MFA。

最簡單的選項是讓啟用特殊權限角色的使用者依賴 Azure MFA。若要這樣做，請先檢查這些使用者已獲授權；必要時，針對 Azure MFA 進行註冊。如需執行此動作的詳細資訊，請參閱[開始在雲端中使用 Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users)。建議 (但非必要) 設定 Azure AD，在這些使用者登入時，強制為他們執行 MFA。這是因為 MFA 檢查將是由 Azure AD PIM 本身所進行。

或者，如果使用者驗證內部部署，您可以讓您的身分識別提供者負責進行 MFA。例如，如果您已在存取 Azure AD 之前將 Active Directory 同盟服務設定為需要以智慧卡為基礎的驗證，[使用 Azure Multi-Factor Authentication 與 AD FS 保護雲端資源](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md)包含用來設定 AD FS 來將宣告傳送至 Azure AD 的相關指示。當使用者嘗試啟動角色時，Azure AD PIM 將會在其收到適當宣告時，接受系統已針對使用者驗證過 MFA。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->