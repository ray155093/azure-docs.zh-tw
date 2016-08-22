<properties
	pageTitle="變更 O365 信賴憑證者信任的簽章雜湊演算法 | Microsoft Azure"
	description="本頁面提供變更與 O365 搭配運作之同盟信任的 SHA 演算法的指導方針"
    keywords="SHA1,SHA256,O365,同盟,aadconnect,adfs,ad fs,變更 sha,同盟信任,信賴憑證者信任"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="samueld"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

#變更 O365 信賴憑證者信任的簽章雜湊演算法

##概觀

AD FS 會將其權杖簽署至 Azure Active Directory，以確保它們無法被竄改。此簽章可以是以 SHA1 或 SHA256 為基礎。Microsoft Azure Active Directory 現在支援以 SHA256 演算法簽署的權杖，並建議將權杖簽署演算法設定為 SHA256 以獲得最高層級的安全性。本文提供將權杖簽署演算法設定為更安全 SHA256 層級的步驟。

##變更權杖簽署演算法

在您依照以下步驟的指示設定簽章演算法之後，AD FS 將會開始使用 SHA-256 來簽署 O365 信賴憑證者信任的權杖。您不需要進行任何額外的組態變更，而且進行這項變更並不會影響存取 Office 365 或其他 Azure AD 應用程式的使用者。

###使用管理主控台

* 在主要 AD FS 伺服器上，開啟 [AD FS 管理主控台]。
* 展開 [AD FS] 節點，然後按一下 [信賴憑證者信任]。
* 在您的 O365/Azure 信賴憑證者信任上按一下滑鼠右鍵，然後選取 [內容]。
* 選取 [進階] 索引標籤，然後為安全雜湊演算法選取 SHA256。
* 按一下 [確定]

![SHA256 簽署演算法 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

###使用 AD FS PowerShell Cmdlet

* 在任何 AD FS 伺服器上，以系統管理員權限開啟 PowerShell
* 使用 Set-AdfsRelyingPartyTrust Cmdlet 來設定安全雜湊演算法

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

##另請參閱

* [使用 AAD Connect 修復 O365 信任](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0810_2016---->