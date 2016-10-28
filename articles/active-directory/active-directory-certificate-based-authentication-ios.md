<properties 
    pageTitle="在 iOS 上開始使用憑證式驗證 | Microsoft Azure" 
    description="了解如何使用 iOS 裝置設定方案中的憑證式驗證" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/02/2016" 
    ms.author="markvi" />



# 在 iOS 上開始使用憑證式驗證 - 公開預覽

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


本主題說明如何為 Office 365 企業版、商務版及教育版方案的租用戶使用者，在 iOS 裝置上設定和使用憑證式驗證 (CBA)。

在將您的 Exchange Online 帳戶連線到下列各項時，CBA 可讓您由 Azure Active Directory 透過用戶端憑證在 Android 或 iOS 裝置上驗證您的身分︰

- Office 行動應用程式，例如 Microsoft Outlook 與 Microsoft Word
- Exchange ActiveSync (EAS) 用戶端

設定這項功能之後，就不需要在行動裝置上的特定郵件和 Microsoft Office 應用程式中，輸入使用者名稱和密碼的組合。
 

## 支援的案例和需求  



### 一般需求 


本主題中的所有案例，皆必須進行下列工作︰

- 存取憑證授權單位，以發行用戶端憑證。

- 務必要在 Azure Active Directory 中設定憑證授權單位。您可以在[開始使用](#getting-started)一節中，找到有關如何完成設定的詳細步驟。

- 務必要在 Azure Active Directory 中設定根憑證授權單位和任何中繼憑證授權單位。

- 每個憑證授權單位都必須有一份可透過網際網路對應 URL 來參考的憑證撤銷清單 (CRL)。

- 務必發行用戶端憑證，以供進行用戶端驗證。


- (僅 Exchange ActiveSync 用戶端適用) 用戶端憑證必須將 Exchange Online 中可路由傳送的使用者電子郵件地址，放在 [主體別名] 欄位的 [主體名稱] 或 [RFC822 名稱] 值中。Azure Active Directory 要將 RFC822 值對應到目錄中的 [Proxy 位址] 屬性。



### Office 行動應用程式支援 

| 應用程式 | 支援 |
| ---                       | ---          |
| Word / Excel / PowerPoint | ![勾選][1] |
| OneNote | ![勾選][1] |
| OneDrive | ![勾選][1] |
| Outlook | 敬請期待 |
| Yammer | ![勾選][1] |
| 商務用 Skype | 敬請期待 |


### 需求  

裝置作業系統版本必須是 iOS 9 和更新版本

必須設定同盟伺服器。

iOS 上的 Office 應用程式都需要 Azure Authenticator。

ADFS 權杖必須要有下列宣告，Azure Active Directory 才能撤銷用戶端憑證︰

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (用戶端憑證序號)

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (用戶端憑證簽發者字串)

如果 ADFS 權杖 (或任何其他 SAML 權杖) 中有上述宣告，Azure Active Directory 就會將這些宣告新增至重新整理權杖。當需要驗證重新整理權杖時，這項資訊會用於檢查撤銷。

最佳做法是應該以下列各項來更新 ADFS 錯誤頁面︰

- 在 iOS 上安裝 Azure Authenticator 的需求

- 如何取得使用者憑證的指示。

如需詳細資訊，請參閱[自訂 AD FS 登入頁面](https://technet.microsoft.com/library/dn280950.aspx)。



### Exchange ActiveSync 用戶端支援 


iOS 9 或更新版本支援原生 iOS 郵件用戶端。針對其他所有 Exchange ActiveSync 應用程式，若要判斷這項功能是否受支援，請連絡您的應用程式開發人員。



## 開始使用 


您需要在 Azure Active Directory 中設定憑證授權單位才能開始使用。請為每個憑證授權單位上傳下列各項︰

- 憑證的公開部分 (「.cer」格式)

- 憑證撤銷清單 (CRl) 所在的網際網路對應 URL
 

以下是憑證授權單位的結構描述︰

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


若要上傳資訊，您可以透過 Windows PowerShell 使用 Azure AD 模組。以下是新增、移除或修改憑證授權單位的範例。



### 設定 Azure AD 租用戶以進行憑證式驗證 

1. 以系統管理員權限啟動 Windows PowerShell。

2. 安裝 Azure AD 模組。您必須安裝 [1\.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) 版或更新版本。

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. 連線到目標租用戶︰

        Connect-AzureAD 

### 加入新的憑證授權單位

1. 設定憑證授權單位的各項屬性，然後將它新增至 Azure Active Directory：

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. 取得憑證授權單位︰

        Get-AzureADTrustedCertificateAuthority 


### 擷取憑證授權單位清單

為租用戶擷取目前儲存在 Azure Active Directory 的憑證授權單位︰

        Get-AzureADTrustedCertificateAuthority 


### 移除憑證授權單位

1.	擷取憑證授權單位︰

		$c=Get-AzureADTrustedCertificateAuthority 


2. 移除憑證授權單位的憑證︰

		Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### 修改憑證授權單位 

1.	擷取憑證授權單位︰

		$c=Get-AzureADTrustedCertificateAuthority 


2. 修改憑證授權單位的屬性︰

		$c[0].AuthorityType=1 

3. 設定**憑證授權單位**：

		Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## 測試 Office 行動應用程式  

若要在行動 Office 應用程式上測試憑證驗證︰

1.	在測試裝置上，從 App Store 安裝 Office 行動應用程式 (例如 OneDrive)。

2.	請確認已將使用者憑證佈建到測試裝置。

3.	啟動應用程式。

4.	輸入您的使用者名稱，然後挑選想要使用的使用者憑證。

您應該可以順利登入。





## 測試 Exchange ActiveSync 用戶端應用程式

若要透過憑證式驗證來存取 Exchange ActiveSync，必須要有包含用戶端憑證的 EAS 設定檔供應用程式使用。EAS 設定檔必須包含下列資訊：

- 要用於驗證的使用者憑證

- EAS 端點必須是 outlook.office365.com (目前只在 Exchange Online 多租用戶環境支援這項功能)

您可以透過採用 MDM (例如 Intune)，在裝置上設定和放置 EAS 設定檔，或以手動方式將憑證放在裝置的 EAS 設定檔中。

### 在 iOS 上測試 EAS 用戶端應用程式 

若要在 iOS 9 或更新版本上，利用原生的郵件應用程式來測試憑證驗證︰

1.	設定符合上述需求的 EAS 設定檔。

2.	在 iOS 裝置上安裝設定檔 (使用像是 Intune 或 Apple Configurator 應用程式的 MDM)

3.	將設定檔適當安裝之後，開啟原生的郵件應用程式，然後確認正在同步處理郵件



## 撤銷

若要撤銷用戶端憑證，Azure Active Directory 會從和憑證授權單位資訊一起上傳的 URL 中，擷取憑證撤銷清單 (CRL) 並加以快取。在 CRL 中，上次發佈的時間戳記 ([生效日期] 屬性) 是用來確保 CRL 依然有效。定期參考 CRL 以撤銷對清單所列憑證的存取權。

如果需要立即撤銷 (例如，使用者遺失裝置)，可以讓使用者的授權權杖失效。使用 Windows PowerShell 設定這位特定使用者的 **StsRefreshTokenValidFrom** 欄位，即可讓授權權杖失效。您必須為想要撤銷其存取權的每位使用者更新其 **StsRefreshTokenValidFrom** 欄位。
 
為了確保撤銷持續有效，您必須將 CRL 的 [生效日期] 設定為 **StsRefreshTokenValidFrom** 所設值之後的日期，並確保有問題的憑證位於 CRL 中。
 
下列步驟概述藉由設定 **StsRefreshTokenValidFrom** 欄位，來更新授權權杖並讓它失效的程序。

1. 使用管理員認證連線到 MSOL 服務：

		$msolcred = get-credential 
		connect-msolservice -credential $msolcred 

1.	擷取使用者目前的 StsRefreshTokensValidFrom 值︰

		$user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
		$user.StsRefreshTokensValidFrom 


1.	將目前的時間戳記設定為使用者新的 StsRefreshTokensValidFrom 值︰

		Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


您設定的日期必須是未來的日期。如果不是未來的日期，則不會設定 **StsRefreshTokensValidFrom** 屬性。如果是未來的日期，才會將 **StsRefreshTokensValidFrom** 設定為目前的時間 (而非 Set-MsolUser 命令指示的日期)。



<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

<!---HONumber=AcomDC_0803_2016-->