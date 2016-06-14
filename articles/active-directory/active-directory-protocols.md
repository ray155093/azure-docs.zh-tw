<properties
	pageTitle="Azure Active Directory 驗證通訊協定 | Microsoft Azure"
	description="這篇文章提供 Azure Active Directory 支援的各種驗證和授權通訊協定的概觀。"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Active Directory 驗證通訊協定

Azure Active Directory (Azure AD) 支援數個最常用的驗證和授權通訊協定。

在此文章集合中，我們將查看 Azure AD 中所支援的通訊協定和其實作。我們會有範例要求和回應，而因為我們要直接與通訊協定進行整合，這些文章大多與語言無關。

- [Azure AD 中的 OAuth 2.0](active-directory-protocols-oauth-code.md)：了解有關 Azure AD 中 OAuth2.0 的授權授與流程和它的實作。
- [Open ID Connect 1.0](active-directory-protocols-openid-connect-code.md)：了解如何在 Azure AD 中使用 OpenID Connect 驗證通訊協定。
- [SAML 通訊協定參考](active-directory-saml-protocol-reference.md)：了解如何使用 SAML 通訊協定在 Azure AD 中支援[單一登入](active-directory-single-sign-on-protocol-reference.md)和[單一登出](active-directory-single-sign-out-protocol-reference.md)。


## 參考和疑難排解

這系列文章提供額外的資訊可能有助於您針對 Azure AD 應用程式的問題進行疑難排解，以及協助您深入了解 Azure AD。

- [同盟中繼資料](active-directory-federation-metadata.md)：了解如何尋找並解譯 Azure AD 所產生的中繼資料文件。
- [支援的權杖和宣告類型](active-directory-token-and-claims.md)：了解權杖中 Azure AD 所發出的不同宣告。
- [在 Azure AD 中簽署金鑰變換](active-directory-signing-key-rollover.md)：了解 Azure AD 的簽署金鑰變換模式，以及如何針對最常見的應用程式案例更新金鑰。
- [針對驗證通訊協定進行疑難排解](active-directory-error-handling.md)：了解如何解譯和解決使用 OAuth 2.0 和 Azure AD 時最常見的錯誤。
- [Azure AD 中 OAuth 2.0 的最佳作法](active-directory-oauth-best-practices.md)：了解在 Azure AD 中使用 OAuth 2.0 時的最佳作法，並避免常見的陷阱。

<!---HONumber=AcomDC_0601_2016-->