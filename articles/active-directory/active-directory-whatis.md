---
title: "什麼是 Azure Active Directory？"
description: "使用 Azure Active Directory 將現有的內部部署身分識別延伸至雲端，或開發 Azure AD 整合式應用程式。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 21cd9fa752f6f3bf2e3aab572ad3336fbd2bee92
ms.openlocfilehash: 067bef695d8d6a340f7974478e6580b10a1e8dc8


---
# <a name="what-is-azure-active-directory"></a>什麼是 Azure Active Directory？
Azure Active Directory (Azure AD) 是 Microsoft 的多租用戶雲端型目錄和身分識別管理服務。

對於 IT 系統管理員而言，Azure AD 提供經濟實惠、易於使用的解決方案，讓員工和企業合作夥伴能使用單一登入 (SSO) 功能來存取 [數千個雲端 SaaS 應用程式](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx) ，例如 Office365、Salesforce.com、DropBox 和 Concur。

對於應用程式開發人員而言，Azure AD 可讓您專注於建置應用程式，快速而簡單地整合數百萬個全球各地組織所使用的世界級身分識別管理解決方案。

Azure AD 也包含一組完整的身分識別管理功能，包括多重要素驗證、裝置註冊、自助式密碼管理、自助式群組管理、特殊權限的帳戶管理、角色型存取控制、應用程式使用量監視、豐富的稽核，以及安全性監視和警示。 這些功能可以協助保護雲端型應用程式的安全、簡化 IT 程序、降低成本，以及協助確保達到公司的規範目標。

此外，只要按 [四次按鍵](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx)，Azure AD 就可以與現有的 Windows Server Active Directory 整合，讓組織能夠運用現有的內部部署身分識別投資，來管理雲端型 SaaS 應用程式的存取權。

如果您是 Office365、Azure 或 Dynamics CRM Online 的客戶，您可能不知道您已經在使用 Azure AD。 每個 Office365、Azure 和 Dynamics CRM 租用戶實際上都已經是 Azure AD 租用戶。 只要您願意，您就可以開始使用該租用戶來為上千種整合了 Azure AD 的其他雲端應用程式管理存取權！

![Azure AD Connect 堆疊](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Azure AD 有多可靠？
Azure AD 經悉心設計，不但支援多租用戶、地理分布周全，更具有高可用性，表示您可以依賴 Azure AD 來應付最重要的商務需求。 使用世界各地具自動容錯移轉的 28 個資料中心，您能夠輕易感受到 Azure AD 非常可靠，即使資料中心中斷，您的目錄資料至少在兩個以上地域分散的資料中心都有留存複本，而且可讓您立即存取。

如需詳細資料，請參閱 [服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

## <a name="what-are-the-benefits-of-azure-ad"></a>Azure AD 有哪些優點？
您的組織可以使用 Azure AD 在許多方面提升員工生產力、簡化 IT 程序、改善安全性並降低成本：

* 快速採用雲端服務，提供員工和合作夥伴簡單的單一登入體驗，這是由 Azure AD 提供、完全自動的 SaaS 應用程式存取管理和佈建服務功能。
* 讓員工可以在任何地方，使用他們所喜愛的裝置工作，存取世界級雲端應用程式和自助式功能。
* 輕鬆安全地管理員工和廠商對貴公司社交媒體帳戶的存取權限。
* 利用 Azure AD 多重要素驗證和條件式存取，改善應用程式的安全性。
* 實施具一致性的自助式應用程式存取管理，讓企業主能夠快速採取行動，同時能夠降低 IT 成本和額外負荷。
* 利用安全性報告和監視，監視應用程式使用量並保護您的企業免於受到嚴重的威脅。
* 為內部部署應用程式確保行動 (遠端) 存取的安全性。

## <a name="how-does-azure-ad-compare-to-on-premises-active-directory-domain-services-ad-ds"></a>Azure AD 與內部部署 Active Directory Domain Services (AD DS) 的比較為何？

Azure Active Directory (Azure AD) 和內部部署 Active Directory (Active Directory Domain Services 或 AD DS) 是儲存目錄資料及管理使用者與資源間通訊的系統，包括使用者登入程序、驗證和目錄搜尋。

AD DS 是 Windows Server 上的伺服器角色，這表示它可以部署在實體或虛擬機器上。 它具有以 X.500 為基礎的階層式結構。 它會將 DNS 用於尋找物件 (可與使用 LDAP 互動)，而且主要使用 Kerberos 進行驗證。 除了讓機器加入網域以外，Active Directory 還可啟用組織單位 (OU) 和群組原則物件 (GPO)，而且網域之間會產生信任。

Azure AD 是多客戶公用目錄服務，這表示您可以在 Azure AD 中針對雲端伺服器和應用程式 (例如 Office 365) 建立租用戶。 使用者和群組會建立於單層式結構中 (不含 OU 或 GPO)。 驗證會透過通訊協定 (如 SAML、WS-同盟和 OAuth) 執行。 您可以查詢 Azure AD，但不是使用 LDAP，而必須使用名為 AD Graph API 的 REST API。 這些全都透過 HTTP 和 HTTPS 進行。

您可以使用 Azure AD Connect 來同步處理內部部署身分識別與 Azure AD。

### <a name="authentication-and-authorization-details"></a>驗證和授權詳細資料

| Azure AD                                   | 內部部署 AD DS |
|--------------------------------------------|-------------------|
| <li>   SAML      <li>   WS-同盟    <li>   與支援的認證互動  <li>   OAuth 2.0   <li>   OpenID Connect                          | <li>   SAML   <li>   WS-同盟  <li>   NTLM  <li>   Kerberos   <li>   MD5   <li>   基本          |

### <a name="object-repository-details"></a>物件儲存機制詳細資料

| Azure AD                                          | 內部部署 AD DS |
|---------------------------------------------------|-------------------|
| 透過 Azure AD Graph 和 Microsoft Graph 存取 | X.500 LDAP    |

### <a name="programmatic-access-details"></a>以程式設計方式存取詳細資料

| Azure AD                        | 內部部署 AD DS |
|---------------------------------|-------------------|
| MS/Azure AD Graph REST API | LDAP          |

### <a name="sso-to-applications-details"></a>SSO 應用程式詳細資料

| Azure AD           | 內部部署 AD DS   |
|--------------------|---------------------|
| <li>   OpenID Connect  <li>   SAML          | <li>   SAML      <li>   WS-同盟   <li>   Open-ID connect  |

### <a name="access-management-details"></a>存取管理詳細資料

| Azure AD         | 內部部署 AD DS     |
|------------------------------------|------------------------------------------------------------------------|
| <li>   資源定義的範圍和角色型存取控制   <li>   用戶端定義委派和應用程式權限        <li>   同意架構 (依照資源/用戶端的定義/要求，強制執行適當的使用者/系統管理員同意)  <li>   透過應用程式角色，可以個別套用或套用於各群組，支援︰   <ul> <li>   系統管理員管理       <li>   自助式應用程式存取  <li>   使用者同意 </ul>         | <li>   透過 ACL，可以個別套用或套用於各群組，支援︰  <ul><li>   系統管理員管理  </ul>                                                 |

### <a name="group-management-details"></a>群組管理詳細資料

| Azure AD                          | 內部部署 AD DS                                 |
|-----------------------------------|---------------------------------------------------|
| <li>   系統管理員管理    <li>   規則/動態管理   <li>   自助式群組管理  | <li>   系統管理員管理      <li>   需要外部系統 (FIM 或其他)  <ul><li>   規則/動態管理      </ul>                 |

### <a name="supported-credentials-details"></a>支援的認證詳細資料

| Azure AD           | 內部部署 AD DS  |
|--------------------|--------------------|
| <li>   使用者名稱 + 密碼  <li>   智慧卡     | <li>   使用者名稱 + 密碼  <li>   智慧卡     |


## <a name="how-can-i-get-started"></a>如何開始使用？
* 如果您是 IT 管理員：
  * [試試看！](https://azure.microsoft.com/trial/get-started-active-directory/)  - 您可以立即註冊免費 30 天的試用版，使用此連結不到 5 分鐘即可部署第一個雲端解決方案
  * 閱讀〈開始使用 Azure AD〉了解秘訣和訣竅，獲得 Azure AD 租用戶並快速執行
* 如果您是開發人員：
  * 請查看我們的 Azure Active Directory 的 [開發人員手冊](active-directory-developers-guide.md)
  * [開始使用試用版](https://azure.microsoft.com/trial/get-started-active-directory/) – 立即註冊免費 30 天的試用版，並開始整合您的應用程式與 Azure AD

## <a name="where-can-i-learn-more"></a>哪裡可以深入了解？
我們有無數的絕佳線上資源，協助您詳細了解 Azure AD。 以下是一些很棒的文章，讓您快速入門：

* [使用 Azure AD Connect 啟用目錄的混合式管理](active-directory-aadconnect.md)
* [為連線過的項目提供額外的安全性](../multi-factor-authentication/multi-factor-authentication.md)
* [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)
* [開始使用 Azure AD 報告](active-directory-reporting-getting-started.md)
* [從任何地方管理您的密碼](active-directory-passwords.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)
* [如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)
* [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md)
* [什麼是 Microsoft Azure Active Directory 授權？](active-directory-licensing-what-is.md)
* [如何探索組織內使用未經批准的雲端應用程式](active-directory-cloudappdiscovery-whatis.md)



<!--HONumber=Nov16_HO4-->


