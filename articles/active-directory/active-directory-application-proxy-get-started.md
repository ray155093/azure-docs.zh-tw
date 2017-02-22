---
title: "如何為內部部署應用程式提供安全的遠端存取"
description: "涵蓋如何使用 Azure AD 應用程式 Proxy 為您的內部部署應用程式提供安全的遠端存取。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2e7815702f2d2f4ce935826c4769838727a83696
ms.openlocfilehash: 7d1be1dea6ed4ecda196743f592456a5b977e9b0


---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>如何為內部部署應用程式提供安全的遠端存取
> [!NOTE]
> 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。
> 
> 

現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們想要在自己的裝置上工作，不論這些裝置是平板電腦、手機或膝上型電腦。 而且他們期望能夠存取其所有的應用程式︰雲端中的 SaaS 應用程式以及內部部署的公司應用程式。 傳統上，提供內部部署應用程式的存取權會涉及虛擬私人網路 (VPN)、非軍事區 (DMZ)，或內部部署的反向 Proxy。 這些解決方案不僅複雜且難以確保安全，而且設定及管理成本也很高。

還有更好的辦法！

在行動至上、雲端至上的世界裡，現代化的員工需要現代化的遠端存取解決方案。 Azure AD 應用程式 Proxy 是 Azure Active Directory Premium 產品的一項功能，並提供遠端存取做為服務。 這表示它很容易部署、使用和管理。

## <a name="what-is-azure-active-directory-application-proxy"></a>什麼是 Azure Active Directory 應用程式 Proxy？
Azure AD 應用程式 Proxy 針對 Web 應用程式託管的內部部署，提供單一登入 (SSO) 及安全的遠端存取。 這可能包括 SharePoint 網站、Outlook Web Access 或其他任何您擁有的 LOB Web 應用程式。 這些內部部署 Web 應用程式會與 Azure AD (O365 所使用的相同身分識別和控制平台) 整合。 使用者接著可以使用和 O365 以及其他與 Azure AD 整合的 SaaS 應用程式相同的存取方式，來存取內部部署應用程式。 您不需要變更網路基礎結構，或需要 VPN 才能為使用者提供此解決方案。

## <a name="why-is-this-a-better-solution"></a>為什麼這是更好的解決方案？
Azure AD 應用程式 Proxy 可對所有內部部署應用程式提供簡單、安全且符合成本效益的遠端存取解決方案。

Azure AD 應用程式 Proxy：  

* 在雲端中運作，因此可以節省時間和金錢。 內部部署解決方案則需要您設定及維護 DMZ、Edge Server 或其他複雜的基礎結構。  
* 比內部部署解決方案更容易設定和保護，因為您不必開放讓任何輸入連線穿過防火牆。  
* 提供絕佳的安全性。 當您使用 Azure AD 應用程式 Proxy 發佈應用程式時，您可以利用 Azure 中豐富的授權控制項和安全性分析。 這表示您不需要變更任何應用程式，就能為所有現有應用程式取得進階的安全性功能。  
* 讓使用者享有一致的驗證體驗。 單一登入可讓使用者使用一個密碼，就能方便且簡單地存取所需的所有應用程式來發揮生產力。  

## <a name="what-kind-of-applications-work-with-azure-ad-application-proxy"></a>哪種應用程式可與 Azure AD 應用程式 Proxy 搭配運作？
透過 Azure AD 應用程式 Proxy，您可以存取不同類型的內部應用程式︰

* 使用整合式 Windows 驗證來進行驗證的 Web 應用程式  
* 使用表單架構存取的 Web 應用程式  
* 您想要公開給不同裝置上豐富應用程式的 Web API  
* 裝載在遠端桌面閘道之後的應用程式  

## <a name="how-does-the-service-work-with-connectors"></a>服務如何搭配連接器使用？
應用程式 Proxy 的運作方式是透過在網路內部安裝一個稱為連接器的精簡型 Windows Server 服務。 透過此連接器，您就不需要開放任何輸入連接埠，或在 DMZ 中放置任何物件。 如果您的應用程式有大量的流量，您可以新增更多連接器，而且該服務會負責負載平衡。 連接器是無狀態的，而且必要時，會從雲端提取所有內容。

如需有關連接器的詳細資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)。 

當使用者從遠端存取應用程式時，他們會連線到已發佈的端點。 使用者在 Azure AD 中進行驗證，然後透過連接器路由至內部部署應用程式。

 ![Azure AD 應用程式 Proxy 圖表](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. 使用者會透過應用程式 Proxy 存取應用程式，然後被導向 Azure AD 登入頁面進行驗證。
2. 成功登入之後，會產生權杖並傳送給使用者。
3. 使用者會將權杖傳送至應用程式 Proxy，而該應用程式 Proxy 會擷取權杖的使用者主體名稱 (UPN) 和安全性主體名稱 (SPN)，然後將要求導向至連接器。
4. 連接器會代表模擬使用者要求可用於內部 (Windows) 驗證的 Kerberos 票證。 這就是所謂的 Kerberos 限制委派。
5. Active Directory 擷取 Kerberos 票證。
6. 票證會傳送到應用程式伺服器和加以驗證。
7. 回應會透過應用程式 Proxy 傳送給使用者。

### <a name="single-sign-on"></a>單一登入
Azure AD 應用程式 Proxy 會針對使用整合式 Windows 驗證 (IWA) 的應用程式或宣告感知應用程式提供單一登入 (SSO)。 如果您的應用程式使用 IWA，應用程式 Proxy 會模擬使用 Kerberos 限制委派的使用者來提供 SSO。 如果您有信任 Azure Active Directory 的宣告感知應用程式，則可以使用 SSO，因為使用者已經由 Azure AD 驗證。

如需有關 Kerberos 的詳細資訊，請參閱[有關 Kerberos 限制委派 (KCD) 您想要知道的一切](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)。

## <a name="how-to-get-started"></a>如何開始使用
請確定您有 Azure AD 基本或進階的訂用帳戶，以及您是全域管理員的 Azure AD 目錄。 您也需要 Azure AD 基本或進階的授權，目錄系統管理員和使用者才能存取應用程式。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md) 。

設定應用程式 Proxy 是以兩個步驟完成：

1. [啟用應用程式 Proxy 並設定連接器](active-directory-application-proxy-enable.md)。    
2. [發佈應用程式](active-directory-application-proxy-publish.md) ：使用快速且簡單的精靈發佈內部部署應用程式並提供遠端存取。

## <a name="whats-next"></a>後續步驟
應用程式 Proxy 還有其他更多用途：

* [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
* [使用現有的內部部署 Proxy 伺服器](application-proxy-working-with-proxy-servers.md) 
* [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
* [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
* [啟用條件式存取](active-directory-application-proxy-conditional-access.md)

如需最新消息，請查閱 [應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Feb17_HO2-->


