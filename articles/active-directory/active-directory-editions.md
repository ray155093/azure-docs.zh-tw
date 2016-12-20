---
title: "Azure Active Directory 版本 | Microsoft Docs"
description: "本文說明 Azure Active Directory 的免費和付費版本選項。 Azure Active Directory Basic、Azure Active Directory Premium P1 及 Azure Active Directory Premium P2 為付費版本。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: dcaf8939-7633-40a8-bd76-27dedbb6083a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b241d70ab7c7e8468a3f358fef5af47a89d42e56


---
# <a name="azure-active-directory-editions"></a>Azure Active Directory 版本
所有 Microsoft Online 商務服務都依賴 Azure Active Directory (Azure AD) 來進行登入和其他身分識別需求。 如果您訂閱任何 Microsoft Online 商務服務 (例如 Office 365、Microsoft Azure)，您就會取得 Azure AD 並可存取所有免費的功能，如下所述。  

Azure Active Directory 是全方位、高可用性的身分識別和存取管理的雲端解決方案，它結合了核心目錄服務、進階身分識別管制及應用程式存取管理。 Azure Active Directory 同時也提供豐富且標準型的平台，可讓開發人員根據集中式原則和規則，提供對其應用程式的存取控制。 利用 Azure Active Directory 免費版，您可以管理使用者和群組、與內部部署目錄同步處理、取得 Azure、Office 365 和數千個熱門 SaaS 應用程式 (像是 Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox 等) 之間的單一登入。 若要深入了解 Azure Active Directory，請參閱[什麼是 Azure AD？](active-directory-whatis.md)

若要增強您的 Azure Active Directory，您可以使用 Azure Active Directory Basic、Premium P1 及 Premium P2 版本來新增付費功能。 付費版本的 Azure Active Directory 是建立在您現有的免費目錄上，提供的企業級功能跨越自助、增強的監視、安全性報告、Multi-Factor Authentication (MFA) 及您的行動工作力的安全存取。

Office 365 訂用帳戶包含的其他 Azure Active Directory 功能在以下比較表中說明。

> [!NOTE]
> 這兩種版本的詳細價格請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。 目前在中國不支援 Premium P1、Premium P2 及 Azure Active Directory Basic。 如需詳細資訊，請透過 Azure Active Directory 論壇與我們連絡。
>
>

* **Azure Active Directory Basic** - 針對具有雲端優先需求的任務背景工作角色設計，此版本提供以雲端為中心的應用程式存取和自助身分識別管理解決方案。 有了 Azure Active Directory 的 Basic 版本，您可獲得生產力增強和成本節約功能，例如：群組式存取管理、雲端應用程式的自助式密碼重設、Azure Active Directory 應用程式 Proxy (以使用 Azure Active Directory 發佈內部部署 Web 應用程式)，全都由可獲得 99.9% 運作時間的企業級 SLA 支援。
* **Azure Active Directory Premium P1** - Azure Active Directory Premium 版本的設計目的是要協助在身分識別及存取管理需求上有更嚴格要求的組織，此版本除了新增功能豐富的企業級身分識別管理功能之外，也可讓混合式使用者順暢地存取內部部署和雲端功能。 此版本包含資訊背景工作角色和混合環境中身分識別管理員對於應用程式存取、自助身分識別和存取管理 (IAM)、雲端中的身分識別保護和安全性所需的一切。 它支援進階管理和委派資源，例如：動態群組和自助群組管理。 它包含 Microsoft Identity Manager (一項內部部署及身分識別和存取管理套件)，並提供可為您的內部部署使用者啟用自助密碼重設之類解決方案的雲端回寫功能。
* **Azure Active Directory Premium P2** - 這個新方案採用了為所有使用者和系統管理員提供進階保護的設計，此方案不僅包括 Azure AD Premium P1 中的所有功能，也包括新的 Identity Protection 與 Privileged Identity Management。 Azure Active Directory Identity Protection 利用數十億個訊號，針對應用程式及重要公司資料提供以風險為基礎的條件式存取。 此外，我們還使用 Azure Active Directory Privileged Identity Management 來協助您管理及保護授權帳戶，讓您能夠探索、限制和監視系統管理員及他們對資源的存取，並在需要時，提供及時的存取權。  

若要註冊並立即開始使用 Active Directory Premium，請參閱 [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)。

> [!NOTE]
> 許多 Azure Active Directory 功能是透過「隨用隨付」版本提供：
>
> * Active Directory B2C 是面向消費者應用程式適用的身分識別和存取管理解決方案。 如需詳細資料，請參閱： [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Azure Multi-Factor Authentication 可透過每一使用者或每一驗證提供者方式使用。 如需詳細資訊，請參閱 [什麼是 Azure Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)
>
>

## <a name="comparing-generally-available-features"></a>比較正式推出的功能
> [!NOTE]
> 如需這項資料的不同檢視，請參閱 [Azure Active Directory 功能](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx)。
>
>

**常用功能**

* [目錄物件](#directory-objects)
* [使用者/群組管理 (新增/更新/刪除)/以使用者為基礎的佈建、裝置註冊](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)
* [單一登入 (SSO)](#single-sign-on-sso)
* [雲端使用者的自助式密碼變更](#self-service-password-change-for-cloud-users)
* [Connect (可將內部部署目錄延伸至 Azure Active Directory 的同步處理引擎)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)
* [安全性/使用量報告](#securityusage-reports)

**基本功能**

* [以群組為基礎的存取管理/佈建](#group-based-access-managementprovisioning)
* [雲端使用者的自助式密碼重設](#self-service-password-reset-for-cloud-users)
* [創建公司品牌 (登入頁面/存取面板自訂)](#company-branding-logon-pagesaccess-panel-customization)
* [應用程式 Proxy](#application-proxy)
* [SLA 99.9%](#sla-999)

**Premium P1 功能**

* [自助式群組和應用程式管理/自助式應用程式新增/動態群組](#self-service-group)
* [使用內部部署回寫來進行的自助式密碼重設/變更/解除鎖定](#self-service-password-resetchangeunlock-with-on-premises-write-back)
* [Multi-Factor Authentication (雲端與內部部署 (MFA Server))](#multi-factor-authentication-cloud-and-on-premises-mfa-server)
* [MIM CAL + MIM 伺服器](#mim-cal-mim-server)
* [雲端應用程式探索](#cloud-app-discovery)
* [Connect Health](#connect-health)
* [群組帳戶的自動密碼變換](#automatic-password-rollover-for-group-accounts)

**Premium P2 功能**

* [身分識別保護](active-directory-identityprotection.md)
* [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

**Azure Active Directory Join – 僅適用於 Windows 10 的相關功能**

* [將裝置加入 Azure AD、Desktop SSO、適用於 Azure AD 的 Microsoft Passport、系統管理員 Bitlocker 復原](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)
* [透過 Azure AD Join 進行 MDM 自動註冊、自助式 Bitlocker 復原、為 Windows 10 裝置新增其他本機系統管理員](#mdm-auto-enrollment)

## <a name="common-features"></a>常用功能
#### <a name="directory-objects"></a>目錄物件
**類型：** 常用功能

預設使用量配額為 150,000 個物件。 物件是指目錄服務中由其各自的唯一辨別名稱所表示項目。 供驗證之用的使用者項目即為物件的一個例子。 如果您的需求將超出此預設配額，請連絡支援服務。 50 萬個物件的數目限制不適用於 Office 365、Microsoft Intune 或任何其他依賴 Azure Active Directory 提供目錄服務的付費 Microsoft 線上服務。

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| 最多 500,000 個物件 |沒有物件數目限制 |沒有物件數目限制 |Office 365 使用者帳戶沒有物件數目限制 |

#### <a name="usergroup-management-addupdatedelete-user-based-provisioning-device-registration"></a>使用者/群組管理 (新增/更新/刪除), 以使用者為基礎的佈建、裝置註冊
**類型：** 常用功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| ![勾選][12] |![勾選][12] |![勾選][12] |![勾選][12] |

**其他詳細資訊：**

* [管理 Azure AD 目錄](active-directory-administer.md)
* [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)

#### <a name="single-sign-on-sso"></a>單一登入 (SSO)
**類型：** 常用功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| 每位使用者 10 個應用程式 (1) |每位使用者 10 個應用程式 (1) |沒有限制 (2) |每位使用者 10 個應用程式 (1) |

1. Azure AD Free 和 Azure AD Basic 的使用者可使用單一登入功能存取最多 10 個應用程式。
2. 藉由使用應用程式庫功能表中提供的範本，自助整合任何支援 SAML、SCIM 或表單型驗證的應用程式。 如需詳細資訊，請參閱 [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](active-directory-saas-custom-apps.md)。

**其他詳細資訊：**

* [使用 Azure Active Directory (AD) 管理應用程式](active-directory-enable-sso-scenario.md)

#### <a name="self-service-password-change-for-cloud-users"></a>雲端使用者的自助式密碼變更
**類型：** 常用功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| ![勾選][12] |![勾選][12] |![勾選][12] |![勾選][12] |

**其他詳細資訊：**

* [如何更新自己的密碼](active-directory-passwords-update-your-own-password.md)

#### <a name="connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory"></a>Connect (可將內部部署目錄延伸至 Azure Active Directory 的同步處理引擎)
**類型：** 常用功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| ![勾選][12] |![勾選][12] |![勾選][12] |![勾選][12] |

**其他詳細資訊：**

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

#### <a name="securityusage-reports"></a>安全性/使用量報告
**類型：** 常用功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| 3 個基本報告 |3 個基本報告 |進階報告 |3 個基本報告 |

**其他詳細資訊：**

* [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)

## <a name="premium-and-basic-features"></a>進階和基本功能
#### <a name="group-based-access-managementprovisioning"></a>以群組為基礎的存取管理/佈建
**類型：** 基本功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾選][12] | ![勾選][12] | &nbsp; |

**其他詳細資訊：**

* [使用群組來管理 SaaS 應用程式的存取權](active-directory-accessmanagement-group-saasapps.md)

#### <a name="self-service-password-reset-for-cloud-users"></a>雲端使用者的自助式密碼重設
**類型：** 基本功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾選][12] |![勾選][12] | ![勾選][12] |

**其他詳細資訊：**

* [使用者和系統管理員的 Azure AD 密碼重設](active-directory-passwords.md)

#### <a name="company-branding-logon-pagesaccess-panel-customization"></a>創建公司品牌 (登入頁面/存取面板自訂)
**類型：** 基本功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾選][12] |![勾選][12] | ![勾選][12] |

**其他詳細資訊：**

* [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)

#### <a name="application-proxy"></a>應用程式 Proxy
**類型：** 基本功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾選][12] | ![勾選][12] | &nbsp; |

**其他詳細資訊：**

* [如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)

#### <a name="sla-999"></a>SLA 99.9%
**類型：** 基本功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![勾選][12] |![勾選][12] | ![勾選][12] |

**其他詳細資訊：**

* [服務等級協定](https://azure.microsoft.com/support/legal/sla/)

## <a name="premium-features"></a>進階功能


#### <a name="a-nameself-service-groupaself-service-group-and-app-managementself-service-application-additionsdynamic-groups"></a><a name="self-service-group"></a>自助式群組和應用程式管理/自助式應用程式新增/動態群組
**類型：** 進階功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12]| &nbsp; |

#### <a name="self-service-password-resetchangeunlock-with-on-premises-write-back"></a>使用內部部署回寫來進行的自助式密碼重設/變更/解除鎖定
**類型：** 進階功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12] | &nbsp; |

#### <a name="multi-factor-authentication-cloud-and-on-premises-mfa-server"></a>Multi-Factor Authentication (雲端與內部部署 (MFA Server))
**類型：** 進階功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![勾選][12] |針對 Office 365 應用程式限定於雲端 |

**其他詳細資訊：**

* [什麼是 Azure Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)


#### <a name="a-namemim-cal-mim-serveramim-cal-mim-server"></a><a name="mim-cal-mim-server"></a>MIM CAL + MIM 伺服器
Microsoft Identity Manager 伺服器軟體的權限會隨 Windows Server 授權 (任何版本) 一起授與。 因為 Microsoft Identity Manager 會在 Windows Server 作業系統上執行，因此只要伺服器執行有效且經過授權的 Windows Server 複本，就可以在該伺服器上安裝並使用 Microsoft Identity Manager。 Microsoft Identity Manager Server 不需要其他個別授權。

**類型：** 進階功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![勾選][12] | &nbsp; |

#### <a name="cloud-app-discovery"></a>雲端應用程式探索
**類型：** 進階功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12] | &nbsp; |

**其他詳細資訊：**

* [使用 Cloud App Discovery 尋找未受管理的雲端應用程式](active-directory-cloudappdiscovery-whatis.md)

#### <a name="azure-ad-connect-health"></a>Azure AD Connect Health
**類型：** 進階功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12] | &nbsp; |

**其他詳細資訊：**

* [在雲端中監視內部部署身分識別基礎結構和同步處理服務。](active-directory-aadconnect-health.md)

#### <a name="automatic-password-rollover-for-group-accounts"></a>群組帳戶的自動密碼變換
**類型：** 進階功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12] | &nbsp; |

#### <a name="identity-protection"></a>身分識別保護
**類型：** 進階功能

| 免費版 | 基本版 | Premium P2 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12] | &nbsp; |

#### <a name="privileged-identity-management"></a>Privileged Identity Management
**類型：** 進階功能

| 免費版 | 基本版 | Premium P2 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12] | &nbsp; |

## <a name="azure-active-directory-join-windows-10-only-related-features"></a>Azure Active Directory Join – 僅適用於 Windows 10 的相關功能
#### <a name="join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery"></a>將裝置加入 Azure AD、Desktop SSO、適用於 Azure AD 的 Microsoft Passport、系統管理員 Bitlocker 復原
**類型：**Azure Active Directory Join – 僅適用於 Windows 10 的相關功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| ![勾選][12] |![勾選][12] |![勾選][12] |![勾選][12] |


#### <a name="a-namemdm-auto-enrollmentamdm-auto-enrollment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join"></a><a name="mdm-auto-enrollment"></a>透過 Azure AD Join 進行 MDM 自動註冊、自助式 Bitlocker 復原、為 Windows 10 裝置新增其他本機系統管理員
**類型：**Azure Active Directory Join – 僅適用於 Windows 10 的相關功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12] | &nbsp; |

#### <a name="enterprise-state-roaming"></a>企業狀態漫遊
**類型：**Azure Active Directory Join – 僅適用於 Windows 10 的相關功能

**可用性：**

| 免費版 | 基本版 | Premium (P1 和 P2) 版 | 僅 Office 365 應用程式 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![勾選][12] | &nbsp; |

**其他詳細資訊：**

* [企業狀態漫遊](active-directory-windows-enterprise-state-roaming-overview.md)

## <a name="azure-ad-preview-features"></a>Azure AD 預覽功能
除了 Free、Basic 及 Premium (P1 和 P2) 版的正式運作功能之外，Azure AD 也提供您一組預覽功能。 您可以使用預覽功能獲得不久的將來可用的功能的印象，並判斷這些功能是否可協助改善您的環境。

**可用的預覽功能：**

* [B2B 共同作業](active-directory-b2b-collaboration-overview.md)
* [管理單位](active-directory-administrative-units-management.md)
* [HR 應用程式整合](active-directory-saas-workday-inbound-tutorial.md)
* [iOS 上的憑證式驗證](active-directory-certificate-based-authentication-ios.md)
* [Android 上的憑證式驗證](active-directory-certificate-based-authentication-android.md)

## <a name="whats-next"></a>後續步驟
* [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
* [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
* [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)

<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png



<!--HONumber=Dec16_HO2-->


