---
title: "建議的安全性原則和組態 | Microsoft docs"
description: "說明 Microsoft 對部署安全電子郵件、文件和應用程式原則和組態的建議和核心概念。"
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
ms.openlocfilehash: 3c8e636921ab269a5314f74789c8144602685412
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---

# <a name="recommended-security-policies-and-configurations"></a>建議的安全性原則和組態
 
## <a name="introduction"></a>簡介

提供可說明對整個企業套用安全性原則和組態之一般 Microsoft 指導方針的建議。 這些建議可確保員工全並發揮生產力。 

### <a name="intended-audience"></a>目標對象

這些建議的目標對象是熟悉 [Exchange Online](https://technet.microsoft.com/library/jj200580.aspx) (Office 365) 和 [Microsoft Enterprise Mobility + Security](http://microsoft.com/ems) 產品的企業基礎結構架構設計師和 IT 專業人員。 這些產品包括但不限於 Azure Active Directory (身分識別)、Microsoft Intune (裝置管理) 和 Azure 資訊保護 (資料保護)。 

### <a name="customer-environment"></a>客戶環境

建議的原則適用於企業組織作業系統完全在 Microsoft 雲端內作業，以及跨內部部署和 Microsoft 雲端部署基礎結構的客戶。 

### <a name="assumptions"></a>假設

許多提供的建議都依賴僅適用於 Enterprise Mobility + Security (EMS) E5 訂用帳戶的服務。 提供的建議假設您擁有完整的 EMS E5 訂用帳戶功能。 

### <a name="caveats"></a>需要注意的事項

您的組織可能會受限於法規或其他合規性需求，包括可能會要求您套用偏離這些建議組態之原則的特定建議。  這些組態建議使用過去並未提供的使用情況控制項。  由於我們認為這些控制項代表安全性和生產力之間的平衡，因此建議使用這些控制項。  

我們已盡力考慮各種組織保護需求。 但是，我們無法考慮所有可能需求或貴組織的所有獨特層面。 請使用本文件作為 Microsoft 和安全生產力企業團隊思考如何正確地在組織中套用原則的指南。 

>[!NOTE]
>如需了解這些建議中所述之保護功能所需核心概念的概觀，請參閱 [EMS 和 Office 365 服務概觀](secure-email-ems-office365-service-descriptions.md)。
>

## <a name="core-concepts"></a>核心概念

如果使用者嘗試完成工作時發生不必要的摩擦，而略過組織安全性原則，則全世界的安全性措施都不再重要。 Azure AD 單一登入 (SSO) 會嘗試將使用者的負擔降到最低。 如此一來，使用者可以維持生產力，同時仍符合組織的存取控制原則。 

### <a name="single-sign-on-authentication"></a>單一登入驗證

下圖說明典型的 SSO 驗證流程：

![終端使用者單一登入體驗](./media/secure-email/typical-authentication-flow.png)

若要開始驗證，用戶端會將認證 (例如使用者名稱和密碼) 和/或之前取得的任何 SSO 構件提交至 Azure AD。 SSO 構件可以是瀏覽器的工作階段權杖或豐富型應用程式的重新整理權杖。 

Azure AD 會驗證認證和/或 SSO 構件，並評估所有適用的原則。 然後其會簽發資源提供者 (圖中的 Exchange Online) 的存取權杖。 Azure AD 也會簽發 SSO 構件，作為允許用戶端在未來的要求中達成 SSO 之回應的一部分。 

用戶端會儲存 SSO 構件，並提交存取權杖作為資源提供者的身分識別證明。 Exchange Online 驗證存取權杖並執行必要檢查之後，便會授與用戶端對電子郵件訊息的存取。

#### <a name="single-sign-on-sso-refresh-tokens"></a>單一登入 (SSO) 重新整理權杖

您可以各種方式達成 SSO。 例如，來自識別提供者的 Cookie 可作為 SSO 構件，以儲存瀏覽器中的使用者登入狀態。 然後未來可能可以嘗試以無訊息模式 (沒有任何認證提示) 登入使用相同識別提供者的應用程式。 

當使用者透過 Azure AD 進行驗證時，系統會透過使用者的瀏覽器和 Azure AD 建立 SSO 工作階段。 SSO 權杖 (採用 Cookie 的形式) 即代表此工作階段。 Azure AD 會使用兩種 SSO 工作階段權杖︰持續性和非持續性。 在登入期間選取 [讓我保持登入] 核取方塊時，系統會依瀏覽器將持續性工作階段權杖儲存為永續性 Cookie。 系統會將非持續工作階段權杖儲存為工作階段 Cookie，並在關閉瀏覽器時遭到終結。 

對於使用新式驗證通訊協定的強固應用程式，例如[OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html) 和 [OAuth 2.0](https://tools.ietf.org/html/rfc6749)，以使用重新整理權杖作為 SSO 構件 (除了先前所述的 SSO Cookie 之外) 來提供 SSO。 應用程式要求新存取權杖時，便會將重新整理權杖提供給授權伺服器。 

重新整理權杖包含有關驗證使用者時使用之驗證方法類型的宣告和屬性。 例如，如果使用者已成功使用多種方法 (使用者名稱和密碼與以電話為基礎的驗證) 進行驗證，重新整理權杖中會具有多重要素驗證 (MFA) 宣告。 此外，其中也可能包含 MFA 有效期限等資料的其他宣告。 

重新整理權杖可讓用戶端取得新存取權杖，而不需要執行新的互動式驗證。 重新整理權杖具有比存取權杖更長的存留期，且可用於兌換取得新存取權和重新整理權杖組。 然後您可以將新取得的重新整理權杖持續用於擷取另一個存取權和重新整理權杖組。 

在重新整理權杖的非使用中時間設定上限到期、重新整理權杖的壽命上限到期或驗證和授權原則需求變更之前，用戶端會持續此 SSO 程序。 簽發原始的重新整理權杖期間會發生此變更。 重大使用者屬性變更 (例如密碼重設) 也需要產生新的驗證權杖。 用戶端必須執行新的互動式驗證才能繼續執行。 基本上，其表示用戶端目前為止未曾發生的 SSO 程序中斷。 

#### <a name="conditional-access"></a>條件式存取

應用程式的授權伺服器 Azure AD 會根據套用至您嘗試存取之資源的任何條件式存取原則評估結果，判斷是否要簽發存取權杖。 如果符合原則需求，則會簽發存取權杖和更新的重新整理權杖。 如果不符合原則，使用者會收到如何符合原則的指示和/或需要完成其他步驟 (包括多重要素驗證 (MFA))。  完成 MFA 之後，系統便會將 MFA 宣告新增至產生的重新整理權杖。  

重新整理權杖中的宣告會隨時間累積。 某些宣告具有到期時間表，到期之後授權檢查期間便不再考慮這些宣告。 這有時會造成未預期的結果。 例如，如果已設定條件式存取原則讓來自外部網路位置的驗證嘗試都需要 MFA。 在此情況下，從外部網路存取資源時，使用者可能有時不會收到預期的 MFA 提示。 此情況的可能原因是使用者離開內部網路之前不久便已執行 MFA。 因此，其存取權杖中具有有效的 MFA 宣告。 此 MFA 宣告符合原則需求，因此 Azure AD 不會向使用者提示其他 MFA 要求。

#### <a name="token-lifetime-policy"></a>權杖存留期原則

除了權杖中個別宣告的到期日之外，權杖本身也具有到期時間。 如先前所述，過期的權杖是一個 SSO 的體驗會中斷的原因。 您可以使用[權杖存留期原則](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)設定 Azure AD 所簽發權杖的存留期。 從上述討論推斷，我們難以捕捉定義 SSO 工作階段的分佈。 例如，豐富型應用程式似乎因各種因素中斷連線時，其可能會影響 SSO 工作階段的存留期。

>[!NOTE]
>Azure AD 全域管理員可以控制重新整理權杖的有效期間和非使用狀態期間。 [Azure Active Directory 中可設定的權杖存留期](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)中已說明有關使用設定之存取權杖和宣告的資訊。
>

#### <a name="primary-refresh-tokens"></a>主要重新整理權杖

目前為止，本文已討論 SSO 在單一用戶端的內容中如何運作。 但在單一應用程式中體驗 SSO 仍無法滿足您的需求。 如今使用者會跨相同應用程式套件中的多個應用程式 (例如 Microsoft Office) 體驗互動式工作流程。 使用者需要跨不相關應用程式的存取權，包括內部開發的 LOB 應用程式。 

傳統上，使用 Windows 整合式驗證 (Kerberos) 的加入網域 Windows 裝置可跨多個應用程式和資源達成高度 SSO 體驗。 這些應用程式包括支援的瀏覽器，例如 Internet Explorer 或 Edge。 Azure AD 領域具有主要重新整理權杖 (PRT) 形式的類比。 

此特殊權限 PRT 權杖僅適用於選取的一組用戶端實體 (例如平台系統元件)。 然後該實體可以允許代理驗證存取其他用戶端應用程式，讓其也可以提供流暢的 SSO 體驗。 

對於 [iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios) 和 [Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android) 裝置上的 Office 365 使用者，我們已透過套用驗證訊息代理程式功能，完成額外工作以減少所需的驗證次數。 此功能已內建於 Microsoft Authenticator 和 Intune 公司入口網站應用程式。

>[!NOTE]
>本文件中所述的建議假設使用者的 iOS 或 Android 裝置已部署其中一個應用程式。 例如，驗證訊息代理程式等應用程式 (Microsoft Authenticator 或 Intune 公司入口網站)。
>

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

由於主體會提供有關其身分識別的多個證明或辨識項，因此多重要素驗證 (MFA) 可提供有關驗證主體的高層級信任。 證明可以是只有主體和授權單位知道的預先建立密碼，或只有主體才會擁有的實際實體。 我們通常會分階段執行 MFA。 首先，其會使用密碼建立身分識別，然後需要不同 (較不容易發生惡意攻擊) 的驗證方法作為第二個要素，反之亦然。

不同的授權單位對的 MFA 或增強式驗證的理解可能稍有不同。 例如，某些授權單位 (更具體地說，是在這些授權單位上設定原則的系統管理員) 可能選擇將以實體智慧卡基礎的驗證理解為 MFA。 即使嚴格來說智慧卡驗證是單一階段驗證。 

您可以將需要實體智慧卡和輸入 PIN (密碼) 才能使用智慧卡的組合理解為 MFA。 但其他人可能選擇對需要執行更繁重驗證方法的頻率採取較寬鬆的態度。 因此其認為在增強式驗證之間執行的標準驗證對通常需要增強式驗證的資源仍有效。 例如，某些組織可能會接受要求使用者每幾個小時或幾天執行一次 MFA。 該時間取決於其所保護之資源的機密性，且使用者嘗試存取資源的實體位置不會變更。

Azure AD 和 AD FS 會使用 MFA 宣告表示是否透過 MFA 執行驗證。 根據預設，透過 [Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud) 或 [Windows Hello 企業版](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)完成驗證時，Azure AD 會簽發具有 MFA 宣告的權杖。 在同盟案例中，Azure AD 會認同來自 AD FS 等同盟識別提供者的 MFA 宣告，並在權杖中保留該 MFA 宣告。 



## <a name="recommended-client-configuration-for-sso-and-conditional-access"></a>適用於 SSO 和條件式存取的建議用戶端組態
本節說明我們建議可為使用者提供最佳 SSO 體驗的預設平台用戶端組態，以及條件式存取的技術必要條件。

### <a name="windows-devices"></a>Windows 裝置
由於 Azure 的設計目的是為內部部署和 Azure AD 提供最流暢的 SSO 體驗，因此我們建議您使用 Windows 10 (1703 或更新版本)。 公司或學校應該將簽發的裝置直接設定為加入 Azure AD；如果組織使用內部部署 AD 網域加入，則應該將這些裝置[設定為透過 Azure AD 自動地以無訊息模式進行註冊](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)。 

對於 BYOD Windows 裝置，使用者可以使用「新增公司或學校帳戶」。 對於 BYOD Windows 裝置，使用者可以使用「新增公司或學校帳戶」。 請注意，Windows 10 上的 Chrome 瀏覽器使用者需要[安裝擴充功能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?utm_source=chrome-app-launcher-info-dialog)，讓這些使用者可以享受與 Edge/IE 相同的流暢登入體驗。 此外，如果您的組織擁有加入網域的 Windows 7 裝置，您可以安裝適用於非 Windows 10 電腦的 Microsoft Workplace Join [封裝](https://www.microsoft.com/download/details.aspx?id=53554)，以透過 Azure AD 註冊裝置。

### <a name="ios-devices"></a>iOS 裝置

我們建議您在部署條件式存取或 MFA 原則之前，先在使用者裝置上安裝 [Microsoft Authenticator 應用程式](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)。 至少應該在要求使用者透過 Azure AD 的新增公司或學校帳戶[註冊其裝置](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time)，或安裝 Intune 公司入口網站應用程式以將其裝置註冊管理時安裝該應用程式。 這取決於設定的條件式存取原則。

### <a name="android-devices"></a>Android 裝置

我們建議使用者在部署條件式存取原則之前，或在特定驗證嘗試期間要求時，先安裝 [Intune 公司入口網站應用程式](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal&hl=en
)和 [Microsoft Authenticator 應用程式](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)。 安裝應用程式之後，系統可能會要求使用者透過 Azure AD 進行註冊或透過 Intune 註冊其裝置。 這取決於設定的條件式存取原則。 

我們也建議屬公司擁有的裝置 (COD) OEM 和廠商都擁有一致標準且支援 Android for Work 或 Samsung Knox，可讓 Intune MDM 原則管理和保護電子郵件帳戶。


### <a name="recommended-email-clients"></a>建議的電子郵件用戶端
下列電子郵件用戶端支援新式驗證和條件式存取。 Azure 資訊保護尚未供所有用戶端使用。

|平台|用戶端|版本/注意事項|Azure 資訊保護|
|:-------|:-----|:------------|:--------------------|
|**Windows**|Outlook|2016, 2013 ([啟用新式驗證]((https://support.office.com/article/Enable-Modern-Authentication-for-Office-2013-on-Windows-devices-7dc1c01a-090f-4971-9677-f1b192d6c910))|是|
|**iOS**|Outlook|[最新](https://itunes.apple.com/us/app/microsoft-outlook-email-and-calendar/id951937596?mt=8)|否|
|**Android**|Outlook|[最新](https://play.google.com/store/apps/details?id=com.microsoft.office.outlook&hl=en)|否|
|**macOS**|即將提供摘要||否|
|**Linux**|不支援||否|

#### <a name="additional-client-software"></a>其他用戶端軟體
您可能需要其他軟體才能存取受 Azure 資訊保護所保護的文件。 請確定您正在透過 Azure 資訊保護，使用[支援的軟體和文件格式](https://docs.microsoft.com/information-protection/get-started/requirements-applications)建立和檢視受保護的文件。


### <a name="security-guidelines"></a>安全性方針

本節包含實作任何後續章節中提供的建議時，應該遵循的一般安全性指導方針。

#### <a name="security-and-productivity-trade-offs"></a>安全性和生產力的取捨

您需要在安全性和生產力之間進行取捨。 我們會透過廣泛使用的「安全性、功能、可用性/易用性」(SFU) 安全性三角理論，協助您了解這些取捨：

![安全性和生產力的取捨](./media/secure-email/security-triad.png)

根據 SFU 三角理論模型，本文件中的建議會根據下列原則： 

* 了解對象 - 透過作業內容/安全性條件賦予更高彈性
* 及時套用安全性原則，並確保其意義

#### <a name="administrators-versus-users"></a>系統管理員與使用者

我們建議您建立安全性群組，其中包含擁有系統管理帳戶或具有取得暫時系統管理帳戶權限資格的所有使用者。 您可以將這些安全性群組用於定義 Azure AD 和 Office 365 系統管理員的特定條件式存取原則。  

原則建議會考慮與帳戶相關聯的權限。 相對於 Exchange Online 和 Office 365，[Office 365 系統管理員](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)角色擁有更多權限。 因此，我們對這些帳戶提供的原則建議會比一般使用者帳戶更嚴格。 提及「系統管理員」的所有原則都是適用於 Office 365 系統管理帳戶的建議原則。

#### <a name="reduce-the-number-of-accounts-with-persistent-admin-access"></a>減少具有持續性系統管理員存取權的帳戶

請使用 Azure AD Privileged Identity Management 減少持續性系統管理帳戶數量。 此外，我們建議 Office 365 系統管理員擁有個別使用者帳戶以供一般非系統管理之用，且只會在必須完成與其工作內容相關聯的作業時，使用系統管理帳戶。


## <a name="tiers-of-security-and-protection"></a>安全性和保護層級
大部分組織都擁有與安全性和資料保護有關的特定需求。 這些需求視產業區隔和組織中的作業內容而有所不同。 例如，法務部門和 Office 365 系統管理員的電子郵件通信可能需要其他營業單位使用者不需要的額外安全性和資訊保護控制項。 

每個產業也擁有自己的一套專門法規。 我們並未提供所有可能的安全性選項清單或根據每個產業區隔或作業內容提供建議，而是為可根據您的需求細微性套用的三個不同安全性和保護層級提供建議：[基準、敏感和嚴格規範](https://go.microsoft.com/fwlink/p/?linkid=841656)。  

**基準**。 我們建議您建立保護資料及存取資料之身分識別和裝置的最低標準。 遵循基準建議可提供符合許多組織需求的強式預設保護。 

**敏感**。 某些客戶擁有必須以較高層級保護的資料子集，或需要以這些較高層級保護所有資料。 您可以在 Office 365 環境中將加強的保護套用至所有或特定資料集。 我們建議您透過相當程度的安全性層級保護可存取敏感性資料的身分識別和裝置。 

**嚴格規範**。 某些組織可能擁有非常少量的高度分類資料、營業秘密或規範資料。 Microsoft 提供可協助組織符合這些需求的功能，包括為身分識別和裝置添加保護。 

### <a name="default-protection-mechanism-recommendations"></a>預設保護機制建議
下表包含每個先前定義之安全性和保護層級的預設保護機制建議：

|保護機制|基準|敏感|嚴格規範|
|:-------------------|:-------|:--------|:---------------|
|**強制執行 MFA**|中等以上登入風險|低等以上登入風險|所有新工作階段|
|**強制執行密碼變更**|適用於高風險使用者|適用於高風險使用者|適用於高風險使用者|
|**強制執行 Intune 應用程式防護**|是|是|是|
|**強制執行 Intune 註冊 (COD)**|需要相容或加入網域的裝置|需要相容或加入網域的裝置|需要相容或加入網域的裝置|

### <a name="device-ownership"></a>裝置擁有權
上表反映許多組織都支援混合的屬公司擁有的裝置 (COD) 及個人或攜帶您自己的裝置 (BYOD)，以發揮跨工作力行動生產力的趨勢。 Intune 應用程式防護原則可確保 COD 和 BYOD 上的電子郵件受保護且不會洩漏至 Outlook 行動裝置應用程式及其他 Office 行動裝置應用程式之外。  

屬公司擁有的裝置必須由 Intune 管理或加入網域才能套用其他保護和控制。  根據資料機密性，您的組織可能選擇對特定使用者族群或特定應用程式不允許使用 BYOD。


## <a name="next-steps"></a>後續步驟
[保護電子郵件的原則和組態](secure-email-recommended-policies.md)

