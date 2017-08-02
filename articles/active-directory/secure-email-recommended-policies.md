---
title: "建議的安全電子郵件原則 | Microsoft Docs"
description: "說明有關如何套用電子郵件原則和組態的 Microsoft 建議原則。"
author: jeffgilb
manager: femila
editor: jsnow
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 1d9577f44adcdefa0b0b8f0f2bafce8d0bda4697
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="recommended-policies"></a>建議的原則
 
本文說明可協助我們的客戶保護組織電子郵件和支援新式驗證和條件式存取之電子郵件用戶端的建議原則。 此外，我們也會討論建議可為使用者提供最佳 SSO 體驗的預設平台用戶端組態，以及條件式存取的技術必要條件。

## <a name="prerequisites"></a>必要條件

實作本文件其餘部分中所述的原則之前，您的組織必須符合幾個必要條件：
* [設定具名網路](https://docs.microsoft.com/azure/active-directory/active-directory-known-networks-azure-portal)。 Azure AD Identity Protection 會收集並分析所有可用的工作階段資料，以產生風險分數。 我們建議您在 Azure AD 具命網路組態中指定網路的組織公用 IP 範圍。 系統會為來自這些範圍的流量指定較低的風險分數，而為來自公司環境之外的流量指定較高的風險分數。
* [透過多重要素驗證 (MFA) 註冊所有使用者](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-manage-users-and-devices)。 Azure AD Identity Protection 會使用 Azure MFA 執行其他安全性驗證。 我們建議您事先要求所有使用者註冊 Azure MFA。
* [啟用加入網域 Windows 裝置的自動註冊裝置](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)。 條件式存取可確保連接至服務的裝置是加入網域或相容的裝置。 若要在 Windows 電腦上支援此功能，則必須透過 Azure AD 註冊裝置。  本文討論如何設定自動裝置註冊。  請注意，您必須使用 AD FS。
* **讓支援團隊做好準備**。 為無法完成 MFA 的使用者做好計劃。 這可以將其新增至原則排除群組，或為其註冊新的 MFA 資訊。 進行這些安全性敏感變更之前，您必須確保實際使用者已提出要求。 要求使用者的經理協助核准是有效的作法。
* [設定讓密碼回寫至內部部署 AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)。 密碼回寫可讓 Azure AD 在偵測到帳戶入侵高風險時，要求使用者變更其內部部署密碼。 您可以兩種方式使用 Azure AD Connect 啟用此功能。 您可以在 Azure AD Connect 安裝精靈的選用功能畫面中啟用密碼回寫，或者您可以透過 Windows PowerShell 啟用它。  
* [啟用新式驗證](https://support.office.com/article/Enable-Exchange-Online-for-modern-authentication-58018196-f918-49cd-8238-56f57f38d662)和[保護傳統端點](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-supported-apps)。  條件式存取可以與運用新式驗證的行動和傳統型應用程式搭配使用。 如果應用程式會使用傳統驗證通訊協定，即使已套用條件，其仍可能會取得存取權。 請務必知道哪些應用程式可以使用條件式存取規則，以及您需要採取哪些步驟來保護其他應用程式進入點。
* 透過啟用 Rights Management [啟用 Azure 資訊保護](https://docs.microsoft.com/information-protection/get-started/infoprotect-tutorial-step1)。 請將 Azure 資訊保護與電子郵件搭配使用以啟動電子郵件分類。 請遵循快速入門教學課程以自訂和發佈原則。  

> [!NOTE]
> 下列建議是以可根據您的需求細微性套用的三個不同安全性和保護層級為基礎：[基準、敏感和嚴格規範](https://docs.microsoft.com/azure/active-directory/secure-email-introduction)。 您透過下載此[ Office 365 身分識別和裝置保護資訊圖](https://go.microsoft.com/fwlink/p/?linkid=841656) (英文) 深入了解。  


## <a name="baseline"></a>基準 
本節說明基準層級之資料、身分識別和裝置保護的安全電子郵件建議。 這些建議應該符合許多組織的預設保護需求。
>[!NOTE]
>下列原則都可互相累加和組建。 每節只會說明適用於每個層級的累加部分。
>

### <a name="conditional-access-policy-settings"></a>條件式存取原則設定

#### <a name="identity-protection"></a>身分識別保護 
您可以為使用者提供單一登入 (SSO) 體驗，如先前幾節中所述。 您只需要根據[風險事件](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)視需要介入。  

* 中等以上登入風險需要進行 MFA
* 需要高風險使用者進行安全密碼變更

>[!IMPORTANT]
>此原則建議需要[密碼同步化](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) (英文) 和[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) (英文)。
>

#### <a name="data-loss-prevention"></a>資料外洩防護 
您的裝置和應用程式管理原則的目標是在裝置遺失或遭竊時防止資料外洩。 您可以透過確保資料存取受 PIN 保護、在裝置上加密資料和在裝置未遭入侵來達成此目的。

|原則建議|說明|
|:--------------------|:----------|
|**需要使用者電腦管理**|要求使用者將其電腦加入 Active Directory 網域，或讓其電腦透過 Intune 或組態管理員註冊管理|
|**透過加入網域電腦的群組原則物件 (GPO) 或組態管理員原則套用安全性設定**|部署設定受管理電腦的原則，以啟用 BitLocker、啟用防毒軟體和啟用防火牆|
|**需要使用者行動裝置管理**|用於存取電子郵件的使用者裝置必須受 Intune 管理，或只透過受 Intune 應用程式防護原則保護的行動電子郵件應用程式 (例如 Outlook Mobile) 存取公司電子郵件|
|**在受管理的裝置上套用 Intune 裝置合規性原則**|將 Intune 裝置合規性原則套用至需要下列項目，且受管理公司的行動裝置和受 Intune 管理的電腦：長度下限為 6 的 PIN、裝置加密、狀況良好的裝置 (不得經過破解或刷機；通過健康情況證明) 且需要 Lookout 或 SkyCure 等協力廠商 MTP 判斷為低風險的裝置 (如果有的話)|
|**適用於在未受管理裝置上執行之受管理應用程式的 Intune 應用程式防護原則**|將 Intune 應用程式防護原則套用至需要下列項目，且在未受管理個人行動裝置上執行的受管理應用程式：長度下限為 6 的 PIN、裝置加密且裝置狀況良好 (不得經過破解或刷機；通過健康情況證明)|

### <a name="user-impact"></a>使用者影響

對大部分組織而言，可讓使用者預期何時和在哪些條件下必須登入 Office 365 才能存取其電子郵件非常重要。  

除了下列情況之外，使用者通常會受益於單一登入 (SSO)： 
* 要求適用於 Exchange Online 的驗證權杖時：
  * 偵測到中等以上登入風險且使用者尚未在目前的工作階段中執行 MFA 時，系統可能會要求使用者進行 MFA。  
  * 使用者必須使用支援 Intune 應用程式防護 SDK 的電子郵件應用程式，或從與 Intune 相容或 AD 加入網域的裝置存取電子郵件。 
* 具有風險的使用者登入並成功完成 MFA 時，系統會要求其變更密碼。

## <a name="sensitive"></a>敏感

本節說明敏感層級之資料、身分識別和裝置保護的安全電子郵件建議。 這些建議適用於擁有必須以較高層級保護的資料子集，或需要以這些較高層級保護所有資料的客戶。 

您可以在 Office 365 環境中將加強的保護套用至所有或特定資料集。 例如，您可以套用原則以確保敏感性資料只會在受保護的應用程式之間共用，以防止資料遺失。 我們建議您透過相當程度的安全性層級保護可存取敏感性資料的身分識別和裝置。 

### <a name="conditional-access-policy-settings"></a>條件式存取原則設定
#### <a name="identity-protection"></a>身分識別保護 

您可以為使用者提供單一登入 (SSO) 體驗，如先前幾節中所述。 您只需要根據[風險事件](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)視需要介入。   

* 低等以上風險的工作階段需要進行 MFA
 * 需要高風險使用者進行安全密碼變更

>[!IMPORTANT]
>此原則建議需要[密碼同步化](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) (英文) 和[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) (英文)。
>

#### <a name="data-loss-prevention"></a>資料外洩防護 

這些裝置和應用程式管理原則的目標是在裝置遺失或遭竊時防止資料外洩。 您可以透過確保資料存取受 PIN 保護、在裝置上加密資料和在裝置未遭入侵來達成此目的。

|原則建議|說明|
|:--------------------|:----------|
|**需要使用者電腦管理**|要求使用者將其電腦加入 Active Directory 網域，或讓其電腦透過 Intune 或組態管理員註冊管理，並在允許電子郵件存取之前確保這些裝置符合原則|
|**透過加入網域電腦的群組原則物件 (GPO) 或組態管理員原則套用安全性設定**|部署設定受管理電腦的原則，以啟用 BitLocker、啟用防毒軟體和啟用防火牆|
|**需要使用者行動裝置管理**|用於存取電子郵件的使用者裝置必須受 Intune 管理，或只透過受 Intune 應用程式防護原則保護的行動電子郵件應用程式 (例如 Outlook Mobile) 存取公司電子郵件|
|**在受管理的裝置上套用 Intune 裝置合規性原則**|將 Intune 裝置合規性原則套用至需要下列項目，且受管理公司的行動裝置和受 Intune 管理的電腦：長度下限為 6 的 PIN、裝置加密、狀況良好的裝置 (不得經過破解或刷機；通過健康情況證明) 且需要 Lookout 或 SkyCure 等協力廠商 MTP 判斷為低風險的裝置 (如果有的話)|
|**適用於在未受管理裝置上執行之受管理應用程式的 Intune 應用程式防護原則**|將 Intune 應用程式防護原則套用至需要下列項目，且在未受管理個人行動裝置上執行的受管理應用程式：長度下限為 6 的 PIN、裝置加密且裝置狀況良好 (不得經過破解或刷機；通過健康情況證明)|

### <a name="user-impact"></a>使用者影響

對大部分組織而言，可讓使用者預期具體上何時和在哪些條件下必須登入 Office 365 電子郵件。 

除了下列情況之外，使用者通常會受益於單一登入 (SSO)： 
* 要求適用於 Exchange Online 的驗證權杖時：
  * 偵測到低等以上登入風險且使用者尚未在目前的工作階段中執行 MFA 時，系統會要求使用者進行 MFA。  
  * 使用者必須使用支援 Intune 應用程式防護 SDK 的電子郵件應用程式，或從與 Intune 相容或 AD 加入網域的裝置存取電子郵件。 
* 具有風險的使用者登入並成功完成 MFA 時，系統會要求其變更密碼。

## <a name="highly-regulated"></a>嚴格規範
本節說明嚴格規範層級之資料、身分識別和裝置保護的安全電子郵件建議。 這些建議適用於可能擁有非常少量的高度分類資料、營業秘密或規範資料的客戶。 Microsoft 提供可協助組織符合這些需求的功能，包括為身分識別和裝置添加保護。 

### <a name="conditional-access-policy-settings"></a>條件式存取原則設定
#### <a name="identity-protection"></a>身分識別保護 

對於嚴格規範層級，Microsoft 建議對所有新工作階段強制執行 MFA。
* 所有新工作階段都需要進行 MFA
* 需要高風險使用者進行安全密碼變更

>[!IMPORTANT]
>此原則建議需要[密碼同步化](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) (英文) 和[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) (英文)。
>

#### <a name="data-loss-prevention"></a>資料外洩防護
這些裝置和應用程式管理原則的目標是在裝置遺失或遭竊時防止資料外洩。 您可以透過確保資料存取受 PIN 保護、在裝置上加密資料和在裝置未遭入侵來達成此目的。

對於嚴格規範層級，我們建議只在與 Intune 相容或加入網域的裝置上執行支援 Intune 應用程式防護原則的應用程式。

|原則建議|說明|
|:--------------------|:----------|
|**需要使用者電腦管理**|要求使用者將其電腦加入 Active Directory 網域，或讓其電腦透過 Intune 或組態管理員註冊管理，並在允許電子郵件存取之前確保這些裝置符合原則|
|**透過加入網域電腦的群組原則物件 (GPO) 或組態管理員原則套用安全性設定**|部署設定受管理電腦的原則，以啟用 BitLocker、啟用防毒軟體和啟用防火牆|
|**需要使用者行動裝置管理**|用於存取電子郵件的使用者裝置必須受 Intune 管理，或只透過受 Intune 應用程式防護原則保護的行動電子郵件應用程式 (例如 Outlook Mobile) 存取公司電子郵件|
|**在受管理的裝置上套用 Intune 裝置合規性原則**|將 Intune 裝置合規性原則套用至需要下列項目，且受管理公司的行動裝置和受 Intune 管理的電腦：長度下限為 6 的 PIN、裝置加密、狀況良好的裝置 (不得經過破解或刷機；通過健康情況證明) 且需要 Lookout 或 SkyCure 等協力廠商 MTP 判斷為低風險的裝置 (如果有的話)|

### <a name="user-impact"></a>使用者影響
對大部分組織而言，可讓使用者預期具體上何時和在哪些條件下必須登入 Office 365 電子郵件。 

* 單一登入工作階段的存留期上限為 1 天。 工作階段到期之後，使用者必須透過 MFA 重新驗證。
* 具有風險的使用者登入並完成 MFA 之後，系統會要求其變更密碼。
* 要求適用於 Exchange Online 的驗證權杖時：
  * 使用者開始新工作階段時，系統都會要求執行 MFA。  
  * 使用者必須使用支援 Intune 應用程式防護 SDK 的電子郵件應用程式
  * 使用者必須從與 Intune 相容或 AD 加入網域的裝置存取電子郵件。 
 
 ## <a name="next-steps"></a>後續步驟
 [部署建議的安全電子郵件原則](secure-email-deploy-recommended-policies.md)

