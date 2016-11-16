---
title: "Azure Active Directory 常見問題集 | Microsoft Docs"
description: "Azure Active Directory 常見問題集可針對 Azure 及 Azure Active Directory 的存取、密碼管理和應用程式存取權等相關問題提供解答。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2016
ms.author: markusvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0f7070d9d691e2471978a2986025ebfdafbeaa7c


---
# <a name="azure-active-directory-faq"></a>Azure Active Directory 常見問題集
Azure Active Directory 是全方位的身分識別即服務 (IDaaS) 解決方案，其涉及範圍橫跨身分識別、存取管理和安全性的所有層面。

如需詳細資訊，請參閱 [什麼是 Azure Active Directory？](active-directory-whatis.md)。

## <a name="accessing-azure-and-azure-active-directory"></a>存取 Azure 和 Azure Active Directory
**問︰當我嘗試在 Azure 傳統入口網站中存取 Azure AD，為何會收到「找不到訂用帳戶」(https://manage.windowsazure.com)？**

**答：** 若要存取 Azure 傳統入口網站，每位使用者都必須擁有 Azure 訂用帳戶的權限。 如果您有付費的 Office 365 或 Azure AD，請瀏覽至 [http://aka.ms/accessAAD](http://aka.ms/accessAAD) 以進行一次性啟用步驟，否則您必須啟用完整的 [Azure 試用版](https://azure.microsoft.com/pricing/free-trial/)或付費訂用帳戶。 

如需詳細資訊，請參閱：

* [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](active-directory-how-subscriptions-associated-directory.md)
* [在 Azure 中管理 Office 365 訂用帳戶的目錄](active-directory-manage-o365-subscription.md)

- - -
**問︰Azure AD、Office 365 和 Azure 之間有何關聯性？**

**答：** Azure Active Directory 可提供您所有 Microsoft Online Services 的常見身分識別和存取功能。 無論您是使用 Office 365、Microsoft Azure、Intune 或其他服務，您都已經在使用 Azure AD 來啟用上述所有服務的登入和存取管理功能。 

事實上，所有您已為其啟用 Microsoft Online Services 的使用者，都會定義為一或多個 Azure AD 執行個體中的使用者帳戶。 您可以為這些帳戶啟用免費的 Azure AD 功能，例如雲端應用程式存取。

此外，Azure AD 付費服務 (例如 Azure AD Basic、Premium、EMS 等) 可透過全方位的企業級管理和安全性解決方案，來彌補其他 Online Services (例如 Office 365 和 Microsoft Azure) 的不足。

- - -
## <a name="getting-started-with-hybrid-azure-ad"></a>開始使用混合式 Azure AD
**問︰如何將內部部署目錄連接至 Azure AD？**

**答︰**您可以使用 **Azure AD Connect** 將內部部署目錄連接至 Azure AD。 

如需詳細資訊，請參閱 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

- - -
**問︰如何設定內部部署目錄與雲端應用程式之間的 SSO？**

**答：** 您只需要在內部部署目錄與 Azure AD 之間設定 SSO。 只要您透過 Azure AD 存取雲端應用程式，該服務就會自動讓使用者使用其內部部署認證正確地進行驗證。

透過同盟解決方案 (例如 ADFS) 或藉由設定密碼雜湊同步處理，就能輕鬆實現從內部部署實作 SSO。 您可以使用 Azure AD Connect 組態精靈輕鬆部署這兩個選項。

如需詳細資訊，請參閱 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

- - -
**問︰Azure Active Directory 是否可為組織中的使用者提供自助入口網站？**

**答：** 是，Azure Active Directory 可提供您 [Azure AD 存取面板](http://myapps.microsoft.com) ，以供使用者進行自助服務和應用程式存取。 如果您是 Office 365 客戶，您可以在 Office 365 入口網站中找到許多相同的功能。 

如需詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

- - -
**問︰Azure AD 是否會協助我管理內部部署基礎結構？**

**答：** 是的，它會。 Azure AD Premium Edition 可提供您 **Connect Health**。 Azure AD Connect Health 可協助您監視和了解內部部署身分識別基礎結構和同步處理服務。  

如需詳細資訊，請參閱 [在雲端中監視內部部署身分識別基礎結構和同步處理服務](active-directory-aadconnect-health.md)。  

- - -
## <a name="password-management"></a>密碼管理
**問︰是否可以使用 Azure AD 密碼回寫，但不使用密碼同步處理？(也就是，我想要使用 Azure AD SSPR 和密碼回寫，但不想將密碼儲存在雲端？)**

**答：** 想要啟用回寫功能，並不需要將 AD 密碼同步處理至 Azure AD。 在同盟環境中，Azure AD SSO 會依靠內部部署目錄來驗證使用者。 在這種情況下，並不需要在 Azure AD 中追蹤內部部署密碼。

- - -
**問︰需要多久時間才能將密碼回寫至 AD 內部部署？**

**答：** 密碼回寫會即時運作。 

如需詳細資訊，請參閱 [開始使用密碼管理](active-directory-passwords-getting-started.md) 

- - -
**問︰是否可以搭配使用密碼回寫和由系統管理員管理的密碼？**

**答：** 是，如果您已啟用密碼回寫，系統管理員所執行的密碼作業便會回寫到內部部署環境。  

如需密碼相關問題的詳細解答，請參閱 [密碼管理常見問題集](active-directory-passwords-faq.md)。

- - -
## <a name="application-access"></a>應用程式存取
**問︰哪裡可以找到與 Azure AD 預先整合的應用程式及其功能的清單？**

**答：** Azure AD 擁有超過 2600 個預先整合的應用程式，其來源包括 Microsoft、應用程式服務提供者或協力廠商。 所有預先整合的應用程式皆支援 SSO。 SSO 可讓您使用組織認證來存取應用程式。 有些應用程式也支援自動化佈建和取消佈建

如需完整的預先整合應用程式清單，請參閱 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)。

- - -
**問︰如果 Azure AD Marketplace 內沒有我需要的應用程式，該怎麼辦？**

**答：** 若您使用 Azure AD Premium，就可以新增及設定您想要的任何應用程式。 視應用程式功能和您的喜好設定而定，您可以設定 SSO 和自動化佈建。  

如需詳細資訊，請參閱：

* [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](active-directory-saas-custom-apps.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](active-directory-scim-provisioning.md) 

- - -
**問︰使用者如何使用 Azure Active Directory 登入應用程式？**

**答：** Azure Active Directory 提供好幾種方式來讓使用者檢視和存取其應用程式，例如︰

* Azure AD 存取面板
* Office 365 應用程式啟動程式
* 直接登入同盟應用程式
* 同盟、密碼或現有應用程式的深層連結

如需詳細資訊，請參閱 [對使用者部署 Azure AD 整合應用程式](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)。

- - -
**問︰Azure Active Directory 可透過哪些不同方式來啟用應用程式的驗證和單一登入？**

**答：** Azure Active Directory 針對驗證和授權支援許多標準化的通訊協定，例如 SAML 2.0、OpenID Connect、OAuth 2.0 和 WS-同盟。 Azure AD 也可針對僅支援表單型驗證的應用程式，支援密碼存放和自動化登入功能。  

如需詳細資訊，請參閱：

* [Azure AD 的驗證案例](active-directory-authentication-scenarios.md)
* [Active Directory 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [單一登入如何搭配 Azure Active Directory 運作？](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**問︰是否可以新增在內部部署中執行的應用程式？**

**答：** Azure AD 應用程式 Proxy 可讓您簡單又安全地存取您選擇的內部部署 Web 應用程式。 正如同存取 Azure Active Directory 中的 SaaS 應用程式一般，相同的方式也可以用來存取這些應用程式。 並不需要 VPN 或變更網路基礎結構。  

如需詳細資訊，請參閱 [如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)。

- - -
**問︰如何對存取特定應用程式的使用者要求 MFA？**

**答：** Azure AD 條件式存取可讓您針對每個應用程式指派唯一的存取原則。 您可以在原則中要求一律要進行 MFA，或在使用者未連線到區域網路時才進行。  

如需詳細資訊，請參閱 [保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取](active-directory-conditional-access.md)。

- - -
**問︰SaaS 應用程式的自動化使用者佈建是什麼？**

**答：** Azure Active Directory 可讓您在許多熱門雲端 (SaaS) 應用程式中自動建立、維護和移除使用者身分識別。 

如需詳細資訊，請參閱 [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)

- - -



<!--HONumber=Nov16_HO2-->


