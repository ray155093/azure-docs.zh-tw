<properties
	pageTitle="Azure Active Directory 常見問題集 | Microsoft Azure"
	description="Azure Active Directory 常見問題集"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="markusvi"/>

# Azure Active Directory 常見問題集

Azure Active Directory (Azure AD) 是一組全方位的雲端式身分識別與存取管理功能，可為您提供身分識別即服務 (IDaaS)。身為 IDaaS 提供者，Azure AD 可讓您控制在電腦網路中「誰可以做什麼」。此外，Azure AD 還可為您提供應用程式的單一登入 (SSO)。透過 SSO，您將可以大幅提升使用者的登入體驗。如需詳細資訊，請參閱[什麼是 Azure Active Directory？](active-directory-whatis.md)。



## 存取 Azure 和 Azure Active Directory

****問︰為何在 Azure 入口網站中找不到 Azure AD (http://portal.azure.com)?**

**答：**Azure AD 系統管理體驗目前仍是在 Azure 傳統入口網站中提供使用 (`http://manage.windowsazure.com`)。整合到 Azure 入口網站的新系統管理體驗預計會在 9 月份推出公開預覽版。如需關於版本的最新資訊，請參閱 [Active Directory 團隊部落格](https://blogs.technet.microsoft.com/ad/)。


---

****問︰當我嘗試在 Azure 傳統入口網站中存取 Azure AD，為何會收到「找不到訂用帳戶」(http://manage.windowsazure.com)?**

**答：**您需要 Azure 訂用帳戶上的權限，如果您有付費的 Office 365 或 Azure AD，請瀏覽至 [http://aka.ms/accessAAD](http://aka.ms/accessAAD) 以進行一次性啟用步驟，否則您必須啟用完整的 [Azure 試用版](https://azure.microsoft.com/pricing/free-trial/)或付費訂用帳戶。

---

**問︰Azure AD、Office 365 和 Azure 之間有何關聯性？**

**答：**Microsoft Azure 是開放式且富有彈性的企業級雲端運算平台和基礎結構，可透過 Microsoft 管理的全球性資料中心網路建置、部署和管理應用程式與服務。例如，Azure 可為您提供軟體即服務 (SaaS) 解決方案，例如 Office 365。Office 365 內含軟體以及服務訂用帳戶，可為您提供生產力軟體和相關線上服務。Azure AD 可為 Office 365 補充一組全方位的雲端式身分識別與存取管理功能，以提供您身分識別即服務 (IDaaS)。如果您擁有 Office 365 訂用帳戶，您也就已經擁有在 Azure 上執行的 Azure AD。


---

**問︰有哪些 Azure AD 功能可供免費使用？**

**答：**Azure 訂用帳戶中的所有常用功能皆可供免費使用。如需這些功能的完整清單，請參閱[常用功能](active-directory-editions.md/#common-features)。



---

問︰**如何將內部部署目錄連接至 Azure AD？**

答︰您可以使用 **Azure AD Connect** 將內部部署目錄連接至 Azure AD。如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。


---

**問︰如何設定內部部署目錄與雲端應用程式之間的 SSO？**

**答：**您只需要在內部部署目錄與 Azure AD 之間設定 SSO。只要雲端應用程式是由 Azure AD 所管理，您便可藉由實作與 ADFS 的同盟或密碼同步處理，來將 SSO 的觸角延伸到內部部署環境。這兩種選項皆包含在 **Azure AD Connect** 之中。如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。
  


---

**問︰Azure AD 是否會協助我管理內部部署基礎結構？**

**答：**是的，它會。Azure AD Premium Edition 可提供您 **Connect Health**。Azure AD Connect Health 可協助您監視和了解內部部署身分識別基礎結構和同步處理服務。如需詳細資訊，請參閱[在雲端中監視內部部署身分識別基礎結構和同步處理服務](active-directory-aadconnect-health.md)。



## 密碼管理

**問︰是否可以使用 Azure AD 密碼回寫，但不使用密碼同步處理？ (也就是，我想要使用 Azure AD SSPR 和密碼回寫，但不想將密碼儲存在雲端？)**

**答：**想要啟用回寫功能，並不需要將 AD 密碼同步處理至 Azure AD。在同盟環境中，Azure AD SSO 會依靠內部部署目錄來驗證使用者。在這種情況下，並不需要在 Azure AD 中追蹤內部部署密碼。

---

**問︰需要多久時間才能將密碼回寫至 AD 內部部署？**

**答：**密碼回寫會即時運作。如需詳細資訊，請參閱[開始使用密碼管理](active-directory-passwords-getting-started.md)


---

**問︰是否可以搭配使用密碼回寫和由系統管理員管理的密碼？**

**答：**是，如果您已啟用密碼回寫，系統管理員所執行的密碼作業便會回寫到內部部署環境。如需密碼相關問題的詳細解答，請參閱[密碼管理常見問題集](active-directory-passwords-faq.md)。



## 應用程式存取


**問︰哪裡可以找到與 Azure AD 預先整合的應用程式及其功能的清單？**

**答：**Azure AD 擁有超過 2600 個預先整合的應用程式，其來源包括 Microsoft、應用程式服務提供者或協力廠商。如需完整的預先整合應用程式清單，請參閱 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)。


---

**問︰如果 Azure AD Marketplace 內沒有我需要的應用程式，該怎麼辦？**

**答：**若您使用 Azure AD Premium，就可以新增及設定您想要的任何應用程式。此外，您還可以根據應用程式功能和您的喜好設定，設定 SSO 和自動化佈建。如需詳細資訊，請參閱：

- [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](active-directory-saas-custom-apps.md)
- [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](active-directory-scim-provisioning.md) 



---

**問︰是否可以新增在內部部署中執行的應用程式？**

**答：**Azure AD 應用程式 Proxy 可讓您透過和 Azure Active Directory 中的 SaaS 應用程式相同的方式，輕鬆存取內部部署 Web 應用程式，而不需要用到 VPN 或變更網路基礎結構。如需詳細資訊，請參閱[如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)。


--- 

**問︰如何對存取特定應用程式的使用者要求 MFA？**

**答：**Azure AD 條件式存取可讓您針對每個應用程式指派唯一的存取原則。您可以在原則中要求一律要進行 MFA，或在使用者未連線到區域網路時才進行。如需詳細資訊，請參閱[保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取](active-directory-conditional-access.md)。

<!---HONumber=AcomDC_0518_2016-->