---
title: "Azure AD 同盟相容性清單"
description: "此頁面包含可用來實作單一登入的非 Microsoft 識別提供者。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: cb06c5bd74816777ec7c5836d362d509a265bd9c
ms.openlocfilehash: 9e16fd3112b093f22ed99829c223a3a80e008f28


---
# <a name="azure-ad-federation-compatibility-list"></a>Azure AD 同盟相容性清單
Azure Active Directory 在不需要任何非 Microsoft 解決方案的情況下，針對 Office 365 和混合式與僅限雲端實作的其他 Microsoft 線上服務，提供單一登入和增強的應用程式存取安全性。 Office 365，就像大部分 Microsoft 線上服務一樣，已經與 Azure Active Directory 整合以提供目錄服務、驗證及授權。 Azure Active Directory 也為數千個 SaaS 應用程式和內部部署 Web 應用程式提供單一登入。 請參閱 Azure Active Directory 應用程式庫以了解支援的 SaaS 應用程式有哪些。

對於已經投資非 Microsoft 同盟方案的組織，此主題包含使用來自下面「Azure Active Directory 同盟相容性清單」中的非 Microsoft 識別提供者，為其 Windows Server Active Directory 使用者在 Microsoft 線上服務設定單一登入的指引。 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer Group](http://oxfordcomputergroup.com/)這家代表 Microsoft 的協力廠商已使用非 Microsoft 識別提供者，針對一組常見的 Azure Active Directory 使用案例測試這些單一登入體驗。

如需如何取得此處所列的協力廠商識別提供者資訊，請與 Oxford Computer Group 連絡： [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com)。

> [!IMPORTANT]
> Oxford Computer Group 僅測試這些單一登入案例的同盟功能。 Oxford Computer Group 並未針對這些單一登入案例的同步處理、雙因素驗證等等元件執行任何測試。
> 
> 此計劃中也未測試使用替代識別碼登入 UPN。
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [Optimal IDM Virtual Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11](#pingfederate-611) 
* [PingFederate 7.2](#pingfederate-72) 
* [PingFederate 8.x](#pingfederate-8x)
* [Centrify](#centrify) 
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [NetIQ Access Manager 4.0.1](#netiq-access-manager-401) 
* [BIG-IP with Access Policy Manager BIG-IP ver.11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
* [VMware Workspace Portal 2.1 版](#vmware-workspace-portal-version-21) 
* [Sign&go 5.3](#signampgo-53) 
* [IceWall Federation Version 3.0](#icewall-federation-version-30) 
* [CA Secure Cloud](#ca-secure-cloud) 
* [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [AuthAnvil Single Sign On 4.5](#authavil-single-sign-on-45)
* [Sailpoint IdentityNow](#sailpoint-identitynow) 

> [!IMPORTANT]
> 由於這些都是協力廠商產品，因此對於與這些識別提供者有關的部署、設定、疑難排解、最佳作法等等問題，Microsoft 並沒有提供支援。 對於和這些識別提供者有關的支援與問題，請直接連絡支援的協力廠商。
> 
> 這些協力廠商識別提供者僅使用 WS-同盟與 WS-Trust 通訊協定，針對與 Microsoft 雲端服務的互通性進行測試。 測試並不包含使用 SAML 通訊協定。
> 
> 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory 可以透過和您的內部部署 Active-Directory 同盟來驗證使用者，或在沒有內部部署同盟伺服器的情況下，透過使用密碼同步來驗證使用者。 

以下是支援此登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |
| 使用 ADAL 的現代應用程式，例如 Office 2016 |支援 |None |

如需有關搭配 AD FS 使用 Azure Active Directory 的詳細資訊，請參閱 [Active Directory 同盟服務 (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

如需搭配密碼同步使用 Azure Active Directory 的相關詳細資訊，請參閱 [Azure AD Connect](active-directory-aadconnect.md)。

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Optimal IDM Virtual Identity Server Federation Services
Optimal IDM Virtual Identity Server Federation Services 可以驗證位於客戶內部部署 Active Directories 中的使用者。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |如需用戶端存取原則的相關詳細資訊，請參閱 [依據用戶端所在位置限制存取 Office 365 服務](https://technet.microsoft.com/library/hh526961.aspx) |

## <a name="pingfederate-611"></a>PingFederate 6.11
PingFederate 6.11 可實作廣泛使用的 WS 同盟身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None (較早版本必須升級至 6.11) |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需有關如何設定此 STS 來為您的 Active Directory 使用者提供單一登入體驗的 PingFederate 指示，請下載 [此處](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>PingFederate 7.2
PingFederate 7.2 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需有關如何設定此 STS 來為您的 Active Directory 使用者提供單一登入體驗的 PingFederate 指示，請參閱 [此處](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8.x
PingFederate 8.x 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需有關如何設定此 STS 來為您的 Active Directory 使用者提供單一登入體驗的 PingFederate 指示，請參閱 [此處](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify
Centrify 可協助在不需要裝載內部部署同盟伺服器的情況下，為 Office 365 提供同盟的單一登入體驗。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |不支援用戶端存取控制 |

如需有關 Centrify 的詳細資訊，請參閱[這裡](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)。|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2
IBM Tivoli Federated Identity Manager 6.2.2 與 IBM Security Access Manager for Microsoft Applications 1.4 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 IBM Tivoli Federated Identity Manager 的相關詳細資訊，請參閱 [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0
SecureAuth IdP 7.2.0 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入體驗和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 SecureAuth 的相關詳細資訊，請參閱 [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293)。

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 累計版本 4
CA SiteMinder Federation 12.52 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 CA SiteMinder 的相關詳細資訊，請參閱 [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0
RadiantOne CFS 3.0 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 RadiantOne CFS 的相關詳細資訊，請參閱 [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/)

## <a name="okta"></a>Okta
Okta 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |整合式 Windows 認證需要設定其他 Web 伺服器與 Okta 應用程式。 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 Okta 的相關詳細資訊，請參閱 [Okta](https://www.okta.com/)

## <a name="onelogin"></a>OneLogin
在 2014 年 5 月測試的 OneLogin 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 OneLogin 的相關詳細資訊，請參閱 [OneLogin](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>NetIQ Access Manager 4.0.1
NetIQ Access Manager 4.0.1 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |*支援 Kerberos Contract |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

*NetIQ 透過 Kerberos Contract 的組態支援 Kerberos 驗證。  如需此組態相關協助，請連絡 NetIQ 或檢視設定指南。 如需 NetIQ Access Manager 的相關詳細資訊，請參閱 [NetIQ Access Manager](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP with Access Policy Manager BIG-IP ver. 11.3x – 11.6x
BIG-IP with Access Policy Manager, (APM) BIG-IP ver. 11.3x – 11.6x 可實作廣泛使用的 SAML 身分識別標準，以提供單一登入體驗和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |不支援 |不支援 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 BIG-IP Access Policy Manager 的相關詳細資訊，請參閱 [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager) 

如需有關如何設定此 STS 來為您的 Active Directory 使用者提供單一登入體驗的 BIG-IP Access Policy Manager 指示，請下載 [此處](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>VMware Workspace Portal 2.1 版
VMware Workspace Portal 2.1 版可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需有關 VMware Workspace Portal 2.1 版的詳細資訊，請從[這裡](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)下載 PDF。

## <a name="signgo-53"></a>Sign&go 5.3
Sign&go 5.3 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |支援 Kerberos Contract |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

Sign&go 5.3 透過 Kerberos Contract 的組態支援 Kerberos 驗證。  如需此組態相關協助，請連絡 Ilex 或檢視 [此處](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)

## <a name="icewall-federation-version-30"></a>IceWall Federation Version 3.0
IceWall Federation Version 3.0 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需有關 IceWall Federation 的詳細資訊，請參閱[這裡](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/)和[這裡](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)。

## <a name="ca-secure-cloud"></a>CA Secure Cloud
CA Secure Cloud 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 CA Secure Cloud 的相關詳細資訊，請參閱 [CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager v7.1
Dell One Identity Cloud Access Manager 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 Dell One Identity Cloud Access Manager 的相關詳細資訊，請參閱 [Dell One Identity Cloud Access Manager](http://software.dell.com/products/cloud-access-manager)

 如需有關如何設定此 STS 來為您的 Office 365 使用者提供單一登入體驗的指示，請參閱 [設定 Office 365 使用者](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5
AuthAnvil Single Sign On 4.5 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需詳細資訊，請參閱 [AuthAnvil 單一登入](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow
Sailpoint IdentityNow 可實作廣泛使用的 WS 同盟/WS-Trust 身分識別標準，以提供單一登入和屬性交換架構。

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需詳細資訊，請參閱 [Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/)。





<!--HONumber=Jan17_HO3-->


