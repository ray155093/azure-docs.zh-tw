<properties
	pageTitle="保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取 | Microsoft Azure"  
    description="透過條件式存取控制，Azure Active Directory 會在驗證使用者時以及允許存取應用程式之前，檢查您挑選的特定條件。一旦符合這些條件，使用者就會通過驗證並獲允許存取應用程式。"  
    services="active-directory" 
	keywords="應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則" 
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/23/2016"
	ms.author="femila"/>


# 保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取  
  
保護對公司資源的存取對每個組織來說相當重要。隨著雲端服務和行動裝置的出現，使用者存取公司資源的方式已經大幅變更。這會導致需要變更如何保護公司資源的相關策略。
  
## 為什麼要使用條件式存取？  
 Azure Active Directory 中的條件式存取控制功能為公司提供一些簡單的方式，可同時保護它們在雲端和內部部署環境中的資源。不論您的需求是像「防止使用竊取的密碼來存取我的資源」還是「必須使用狀況良好、受管理的裝置才能存取我的企業內容」，Azure Active Directory 都能滿足您的需求。

## 如何強制執行條件式存取控制？  
 搭配條件式存取控制時，Azure Active Directory 會在驗證使用者時先檢查您選擇的特定條件，然後才允許存取應用程式。一旦符合這些存取需求之後，使用者就會通過驗證並獲允許存取應用程式。
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## 使用者型資源存取
  
- **使用者屬性**：在使用者屬性層級，您可以套用原則以確保只有已獲授權的使用者可以存取公司資源。
- **使用者的群組成員資格**：您也可以根據使用者的群組成員資格，控制提供給使用者的存取權層級。
- **Multi-Factor Authentication (MFA)**：您也可以強制執行一些原則，以要求使用者必須使用多重要素驗證系統來驗證其身分識別。例如，您可以強制使用者在個人行動電話上確認 PIN 碼，以確保一層額外的安全性。對於未獲授權但已取得有效使用者之使用者名稱和密碼存取權的使用者，MFA 驗證可防止其存取您的資源。
- **登入和使用者風險**︰條件式存取風險原則適用於 Azure AD Identity Protection，並根據風險事件和異常登入活動提供進階保護。
 

## 裝置型條件式存取 

**註冊的裝置**：在裝置層級，您可以設定一些原則來強制要求只有已註冊行動裝置管理 (MDM) 的裝置才能進行存取。您可以使用 Microsoft Intune 來驗證裝置已註冊且相容。裝置層級的條件式存取之後便可確保只有符合您原則 (例如在裝置上強制進行檔案加密) 的裝置會獲允許存取。此外，透過使用 MDM 解決方案，您還可以確保能夠從遠端清除遺失/遭竊裝置上的公司資料。
  

可使用這些原則來設定的存取層級可以套用至雲端或內部部署環境中的資源。雲端中的資源可能是 Office 365 和協力廠商 SaaS 應用程式之類的應用程式。此外，這些也可能是您已裝載於雲端的企業營運應用程式。
  
## 條件式存取 - 內容對應  
下列內容對應列出進一步了解在您目前的部署中啟用條件式存取所需參考的文件


| 案例 | 文章 |
|------------------------------------------------------|----------|
| 根據驗證強度或使用者保護資源 |[根據群組、位置和應用程式敏感性開始使用 Azure AD 連線應用程式的條件式存取](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[支援的應用程式種類](active-directory-conditional-access-supported-apps.md)|
| 保護遺失/遭竊裝置上的公司資料 |[Help protect your data with full or selective wipe using Microsoft Intune (使用 Microsoft Intune 搭配完整或選擇性抹除協助保護您的資料)](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)|
|根據登入風險保護資源 |[Azure AD Identity Protection](active-directory-identityprotection.md) |
| 其他資訊 |[條件式存取常見問題集](active-directory-conditional-faqs.md)<br><br>[技術參考](active-directory-conditional-access-technical-reference.md) |

<!---HONumber=AcomDC_0629_2016-->