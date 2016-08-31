<properties
	pageTitle="Azure Active Directory 條件式存取 | Microsoft Azure"  
    description="透過條件式存取控制，Azure Active Directory 會在驗證使用者時以及允許存取應用程式之前，檢查您挑選的特定條件。一旦符合這些條件，使用者就會通過驗證並獲允許存取應用程式。"  
    services="active-directory" 
	keywords="應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則" 
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="08/17/2016"
	ms.author="markvi"/>


# Azure Active Directory 條件式存取   
  
保護對公司資源的存取對每個組織來說相當重要。隨著雲端服務和行動裝置的出現，使用者存取公司資源的方式已經大幅變更。這導致需要新的安全性方法。
  
## 為什麼要使用條件式存取？  

Azure Active Directory 中的條件式存取控制功能可提供一些簡單的方式，讓公司保護在雲端和內部部署環境中的資源。條件式存取原則可用來協助防範認證遭竊和遭到網路釣魚的風險 (藉由要求使用 Multi-Factor Authentication)，以及協助保護公司資料的安全 (藉由要求使用會授與機密服務存取權的 Intune 受管理裝置)。



## 授權需求

條件式存取是 [Azure AD Premium](http://www.microsoft.com/identity) 的一項功能。所有存取已套用條件式存取原則之應用程式的使用者，都必須要有 Azure AD Premium 授權。您可以透過[未經授權的使用者報告](https://aka.ms/utc5ix)深入了解使用情況。





## 如何強制執行條件式存取控制？  

搭配條件式存取控制時，Azure Active Directory 會在驗證使用者時先檢查您選擇的特定條件，然後才允許存取應用程式。一旦符合這些存取需求之後，使用者就會通過驗證並獲允許存取應用程式。
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## 條件
  
- **群組成員資格**：您可以根據使用者的群組成員資格，控制使用者的存取權層級。

- **位置**︰您可以利用使用者位置來觸發 MFA，並在使用者未在受信任網路時封鎖控制權。

- **裝置平台**︰您可以使用裝置平台類型 (例如 iOS、Android、Windows Mobile 和 Windows) 做為原則的套用條件。

- **裝置啟用**︰裝置原則評估期間會驗證裝置的啟用/停用狀態。透過在目錄中停用遺失或遭竊的裝置，它就不能再用來滿足原則需求。

- **登入和使用者風險**︰條件式存取風險原則適用於 Azure AD Identity Protection，並根據風險事件和異常登入活動提供進階保護。


## 控制
   
- **Multi-Factor Authentication (MFA)**︰您可以使用 MFA 來要求增強式驗證。可由 Azure MFA 或在內部部署的 MFA 提供者使用 ADFS 提供 MFA。對於未獲授權但已取得有效使用者之使用者名稱和密碼存取權的使用者，MFA 驗證可協助防止其存取您的資源。

- **封鎖**︰可依據使用者位置等條件套用存取權。例如，當使用者不是在受信任網路時封鎖存取權。

- **相容裝置**：在裝置層級，您可以設定一些原則，來強制要求只有已加入網域的電腦或已向「行動裝置管理」(MDM) 註冊且符合規範的行動裝置，才能進行存取。Microsoft Intune 可用來檢查裝置相容性並回報給 Azure Active Directory，以在進行應用程式存取時強制要求相容性。
 

## 應用程式

- 可使用這些原則來設定的存取層級可以套用至雲端或內部部署環境中的應用程式和服務。原則會直接套用至網站或服務。然後，就會對瀏覽器存取以及存取服務的應用程式強制執行此原則。您可以在這裡找到可以套用原則的服務清單。


## 裝置型條件式存取

您也可以針對已向 Azure AD 註冊且符合特定條件的裝置，限制對應用程式的存取權。這可用來保護組織資源，讓有效使用者無法從下列裝置存取這些資源：

- 不明/未受管理的裝置
- 不符合貴組織所定義安全性原則的裝置。

您可以根據下列需求來設定原則：

- **已加入網域的裝置** - 您可以設定原則來限制只有已加入內部部署 Active Directory 網域並且也已向 Azure AD 註冊的裝置才能存取。此原則適用於屬於內部部署 Active Directory 網域且已向 Azure AD 註冊的 Windows 桌上型電腦、膝上型電腦或企業平板電腦。如需有關如何設定讓已加入網域的裝置自動向 Azure AD 註冊的詳細資訊，請參閱[如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration-setup.md)。

- **相容裝置** - 您可以設定原則來限制只有在目錄中被管理系統標示為「相容」的裝置才能存取。此原則可確保只有符合安全性原則 (例如在裝置上強制執行檔案加密) 的裝置會獲允許存取。此原則可用來限制來自下列裝置的存取︰

    - 由部署在混合式組態中的 System Center Configuration Manager 2016 所管理的「已加入網域的 Windows 裝置」。

    - 由 Microsoft Intune 或支援的第三方「行動裝置管理」(MDM) 系統所管理的「Windows 10 行動工作或個人裝置」。

    - 由 Microsoft Intune 所管理的「iOS 和 Android 裝置」。


使用者如果存取受裝置型 CA 原則保護的應用程式，必須從符合此原則的裝置進行存取。如果是從不符合此原則的裝置進行存取，則會遭到拒絕。

如需有關如何在 Azure AD 中設定裝置型 CA 原則的資訊，請參閱[如何設定裝置型條件式存取原則來控制對 Azure Active Directory 連線應用程式的存取](active-directory-conditional-access-policy-connected-applications.md)。

## Azure Active Directory 條件式存取的文章索引
  
下列內容對應列出進一步了解在您目前的部署中啟用條件式存取所需參考的文件


### MFA 和位置原則

- [根據群組、位置和 MFA 原則開始使用 Azure AD 連線應用程式的條件式存取](active-directory-conditional-access-azuread-connected-apps.md)

- [支援何種應用程式](active-directory-conditional-access-supported-apps.md)


### 裝置型條件式存取

- [如何設定裝置型條件式存取原則來控制對 Azure Active Directory 連線應用程式的存取](active-directory-conditional-access-policy-connected-applications.md)

- [如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration-setup.md)

- [使用者在存取 Azure AD 裝置型條件式存取受保護應用程式時的補救方式](active-directory-conditional-access-device-remediation.md)

- [要求使用 Microsoft Intune 以協助保護資料](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### 根據登入風險保護資源

[Azure AD Identity Protection](active-directory-identityprotection.md)

### 其他資訊

- [條件式存取常見問題集](active-directory-conditional-faqs.md)
- [技術參考](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0817_2016-->