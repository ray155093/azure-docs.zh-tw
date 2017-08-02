---
title: "EMS 和 Office 365 服務概觀 | Microsoft Docs"
description: "本文提供 EMS 和 Office 365 服務的概觀。"
author: jeffgilb
manager: femila
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 288ac3f9ec6354d4108461f32618eb95d3b3ab14
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="ems-and-office-365-services-overview"></a>EMS 和 Office 365 服務概觀

本文提供所述建議中利用之 EMS 和 Office 365 服務的概觀，並討論了解 Azure 電子郵件保護功能所需的核心概念。 這些功能可讓 Microsoft 雲端企業系統管理員保護公司員工的身分識別和裝置，並控制對傳輸中和靜止之公司資料本身的存取。

## <a name="services"></a>服務 

本文件中參考的服務如下表所述。
 
|服務|說明|
|-------|-----------|
|[Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)|Azure AD 提供一組完整的身分識別管理功能，包括多重要素驗證、裝置註冊、自助式密碼管理、自助式群組管理、角色型存取控制、應用程式使用情況監視、豐富的稽核，以及安全性監視和警示。|
|[Azure AD Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection)|此服務可透過低、中等和高登入風險和使用者風險的條件式存取原則，讓您偵測到影響組織身分識別的潛在弱點，並設定自動化回應。|
|[Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|此服務可讓組織擁有特殊權限作業之持續存取權的人數降到最低；Azure AD Privileged Identity Management 引入合格系統管理員的概念。 合格系統管理員應該是指偶爾需要特殊存取權限而非每一天都需要此權限的使用者。 在使用者需要存取權之前，角色會處於非作用中狀態，然後使用者須完成啟用程序，才能在一段預定的時間內成為作用中的系統管理員。|
|[Azure 資訊保護](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)| Azure 資訊保護是雲端式的解決方案，作為 EMS E5 供應項目的一部分提供，且可協助組織對其文件和電子郵件進行分類、標籤和保護。 定義規則和條件的系統管理員可自動完成此動作，使用者也可以手動方式完成，或在提供使用者建議的情況下組合完成。 您可以使用 Azure 資訊保護標籤，將分類套用至文件和電子郵件。 當您執行此動作時，不論資料儲存位置或共同對象為何，隨時都可識別分類。 <br><br>Azure 資訊保護原則設定會受到 [Azure Rights Management](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms) 保護。 類似於如何套用標籤，不論位置是組織內部或外部、網路、檔案伺服器或應用程式，使用 Rights Management 套用的保護都可持續保護文件和電子郵件。|
|[Microsoft Intune](https://docs.microsoft.com/intune/understand-explore/introduction-to-microsoft-intune)|Intune 是雲端式的企業行動管理 (EMM) 服務，可協助讓員工發揮生產力，同時持續保護公司資料。 Intune 與 Azure AD 緊密整合以進行身分識別和存取控制，且可用於裝置和應用程式管理。 [Intune 的裝置管理](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies)功能可用於設定和保護使用者的裝置，包括 Windows 電腦。 <br><br>Intune 裝置管理功能支援可讓使用者註冊其個人電話、平板電腦或電腦等[自行攜帶裝置 (BYOD)](https://docs.microsoft.com/enterprise-mobility-security/solutions/enable-byod)，也可註冊供管理案例 (例如自動註冊、共用裝置或預先授權的註冊需求組態) 使用的[公司所有裝置 (COD)](https://docs.microsoft.com/enterprise-mobility-security/solutions/issue-corp-devices)。 為了提高安全性，您甚至可以要求 MFA 以註冊裝置。 註冊管理之後，Intune 便可以設定裝置功能和設定，讓您安全地存取公司資源。|


## <a name="ems-concepts"></a>EMS 概念
您應該要熟悉的核心概念和 EMS 功能如下表所述。

|核心概念|說明|
|------------|-----------|
|[Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)|Azure MFA 是 Microsoft 的雙步驟驗證解決方案，有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它可以透過一些驗證方法 (包括電話、文字訊息，或行動應用程式驗證) 來提供強大的驗證功能。|
|[Azure AD 條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)|此 Azure AD 功能可讓您根據特定條件，強制控制環境中對雲端應用程式的存取。 利用控制項，您可以將額外需求繫結到存取，也可以封鎖存取。 條件式存取的實作是以原則為基礎。|
|[Exchange Online 資料外洩防護 (DLP)](https://support.office.com/article/Overview-of-data-loss-prevention-policies-1966b2a7-d1e2-4d92-ab61-42efbb137f5e)|作為 Exchange Online 方案 2 和 Office 365 訂用帳戶進階功能提供的 Exchange Online 資料外洩防護 (DLP) 原則，可讓組織跨 Office 365 識別、監視和自動保護機密資訊。<br><br>您可以透過 Exchange Online DLP 原則跨多個位置識別機密資訊，例如 Exchange Online、SharePoint Online 和商務用 OneDrive。 舉例來說，這些原則可協助您識別包含機密資訊的文件，或防止意外與組織外部人員共用機密資訊。|
|[Exchange 郵件流通/傳輸規則](https://support.office.com/en-US/article/Define-mail-flow-rules-to-encrypt-or-decrypt-email-messages-9B7DAF19-D5F2-415B-BC43-A0F5F4A585E8)|Exchange 郵件流通規則 (也稱為傳輸規則) 會在通過組織的訊息中尋找特定條件，並採取行動。 郵件流通規則就像可用於許多電子郵件用戶端的收件匣規則。 郵件流通規則和在 Outlook 等用戶端應用程式中設定的規則之間的差異，主要是郵件流通規則會對傳輸中的訊息採取行動，而非傳遞訊息之後才開始進行。 郵件流通規則也包含一套更豐富的條件、例外狀況和動作，可讓您彈性地實作許多類型的傳訊原則。|
|[Intune 行動裝置管理](https://docs.microsoft.com/intune/understand-explore/introduction-to-microsoft-intune)|Intune 透過使用在行動作業系統中可用的通訊協定或 API，提供行動裝置管理 (MDM)。 其包括將裝置註冊管理讓 IT 擁有可存取公司服務之裝置的清查、設定裝置以確保其符合公司安全性和健康情況標準、提供可存取公司服務的憑證和 Wi-Fi/VPN 設定檔、報告和衡量裝置對公司標準的合規性，以及從受管理的裝置中移除公司資料等工作。|
|[Intune 應用程式防護原則](https://docs.microsoft.com/intune/deploy-use/protect-app-data-using-mobile-app-management-policies-with-microsoft-intune)|Intune 應用程式防護原則可用於保護已註冊或未註冊管理之行動裝置應用程式裝置中的公司資料。 事實上，即使使用者的行動裝置受其他非 Microsoft MDM 解決方案管理，[Intune 也可協助保護 Office 365 資訊](https://docs.microsoft.com/enterprise-mobility-security/solutions/protect-company-data-without-managing-devices)。 確定員工仍可發揮生產力，同時防止資料故意和意外遺失。 您可以透過實作應用程式層級原則，限制對公司資源的存取，並讓資料保持在 IT 部門的控制範圍內。|
|[Azure AD 權杖存留期](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)|您可以指定 Azure Active Directory (Azure AD) 所簽發的權杖存留期。 不論是針對組織中所有的應用程式、針對多租用戶 (多組織) 應用程式，還是針對組織中特定的服務主體，都可以設定權杖存留期。|
|Microsoft 身分識別代理人|Microsoft 為每個行動平台提供應用程式，允許跨不同廠商的應用程式橋接認證，並允許需要單一安全地方來驗證認證的特殊增強功能。 我們將其稱為訊息代理程式。 在 iOS 和 Android 上，我們會透過 Microsoft Authenticator 和 Intune 公司入口網站應用程式提供這些訊息代理程式。 在 Windows 10 中，內建於作業系統的帳戶選擇器會提供此功能，在技術上稱為 Web 驗證訊息代理程式。|

