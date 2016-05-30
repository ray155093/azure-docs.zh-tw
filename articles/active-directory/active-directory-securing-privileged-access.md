<properties
	pageTitle="保護 Azure AD 的特殊權限存取 | Microsoft Azure"
	description="這個主題說明在 Azure、Azure Active Directory 和 Microsoft Online Services 之間保護特殊權限存取的方法。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor="mwahl"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="kgremban"/>


# 保護 Azure AD 中的特殊權限存取

保護特殊權限存取，是有助保護現代組織企業資產很重要的第一個步驟。組織中大部分或所有企業資產的安全性，取決於操控管理 IT 系統之特殊權限帳戶的完整性。網路攻擊者會以這些帳戶為目標來取得組織資料和系統的存取權。

保護系統管理存取權以對抗堅決的對手，需要將這些系統管理帳戶和系統與風險隔離。更多使用者開始透過雲端服務取得特殊權限存取。這包括 Office365 的全域系統管理員、Azure 訂用帳戶系統管理員和有 VM 或 SaaS 應用程式系統管理存取權限的使用者。

Microsoft 建議您遵循 [Securing Privileged Access (保護特殊權限存取)](https://technet.microsoft.com/library/mt631194.aspx) 的這份藍圖。

這些原則是否適用使用 Azure Active Directory 管理 Azure、Office 365 或其他 Microsoft 服務和應用程式存取權的客戶，取決於使用者帳戶是由 Active Directory 管理驗證，或在 Azure Active Directory 中管理驗證。下列章節提供支援保護特殊權限存取之 Azure AD 功能的詳細資訊。

## Multi-Factor Authentication

若要加強系統管理員驗證的安全性，您應先要求 Multi-Factor Authentication (MFA) 再授與權限。MFA 是除了使用使用者名稱與密碼之外，需要再利用其他方法驗證您的身份的驗證方法。它可以為使用者登入和交易提供第二層安全性。

Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。它可以透過一些簡單的驗證選項，包括電話、文字訊息、行動應用程式通知，或驗證代碼和協力廠商 OATH 權杖，來提供強大的驗證功能。

如需 Azure Multi-Factor Authentication 運作方式的概觀，請參閱以下影片。

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

如需詳細資訊，請參閱 [MFA for Office 365 and MFA for Azure (MFA for Office 365 和 MFA for Azure)](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/)。

## 時間界限權限

某些組織可能會發現它們有太多擁有高權限角色的使用者。使用者可能因為某個特定活動，像是登入服務，而加入角色中，但之後卻不經常使用這些權限。

若要降低權限的曝光時間，並增加其使用的能見度，請限制使用者只在需要執行工作時才 (Just in Time, JIT) 使用其權限。Azure Active Directory 與 Microsoft Online Services 可以使用 [Azure AD Privileged Identity Management (PIM)](http://aka.ms/AzurePIM)。


![PIM 儀表板][2]


## 攻擊偵測

[Azure Active Directory Identity Protection](active-directory-identityprotection.md) 提供您整合檢視會影響組織身分識別的風險事件和潛在弱點。Identity Protection 會根據風險事件，計算每位使用者的使用者風險層級，讓您設定以風險為基礎的原則來自動保護您組織的身分識別。這些原則和 Azure Active Directory 與 EMS 提供的其他條件式存取控制，可以自動封鎖使用者或提供建議，包括重設密碼以及強制 Multi-Factor Authentication。

![Azure AD Identity Protection][3]

## 條件式存取

搭配條件式存取控制時，Azure Active Directory 會在驗證使用者時先檢查您選擇的特定條件，然後才允許存取應用程式。一旦符合這些條件，使用者就會通過驗證並獲允許存取應用程式。


![使用 MFA 設定條件式存取規則][4]


## 角色模型

若要降低安全性風險，您應該檢閱您的角色和委派模型，來容納雲端服務系統管理角色，並將系統管理員可用性併入為主要宗旨。這個模型應該利用 Just in Time 功能。

## 後續步驟

- 啟用 [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- 啟用 [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md)
- 啟用 [Azure AD Identity Protection](active-directory-identityprotection.md)
- 啟用[條件式存取控制](active-directory-conditional-access.md)


如需建置完整安全性藍圖的詳細資訊，請參閱 [Microsoft Cloud Security for Enterprise Architects (Microsoft 的企業架構雲端安全性)](http://aka.ms/securecustomer)一文的＜Customer responsibilities and roadmap＞(客戶責任和藍圖) 一節。如需吸引人的 Microsoft 服務詳細資訊，協助您使用任一這些主題，請連絡您的 Microsoft 代表或瀏覽我們的 [Cybersecurity solutions (網路安全性方案) 網頁](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx)。

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-identityprotection/29.png
[4]: ./media/active-directory-conditional-access/conditionalaccess-saas-apps.png

<!---HONumber=AcomDC_0518_2016-->