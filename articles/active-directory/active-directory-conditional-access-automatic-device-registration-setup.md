<properties
	pageTitle="如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊 | Microsoft Azure"
	description="IT 管理員可以選擇以自動和無訊息的方式，向 Azure Active Directory (Azure AD) 註冊已加入網域的 Windows 裝置。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="markvi"/>



# 如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊 

已加入網域的 Windows 的電腦必須向 Azure AD 註冊，才能啟用 [Azure Active Directory 裝置型條件式存取](active-directory-conditional-access.md)。

更新功能包括：允許使用工作或學校帳戶來取得增強的 Azure AD 應用程式 SSO 體驗、跨裝置進行企業設定漫遊、使用「商務用 Windows 市集」，以及透過 Windows Hello 擁有更強的驗證和便利的登入方式。

> [AZURE.NOTE] 「Windows 10 2015 年 11 月更新版」支援部分增強的使用者體驗，不過「年度更新版」才有完整的「裝置型條件式存取」支援。如需有關條件式存取的詳細資訊，請參閱 [Azure Active Directory 裝置型條件式存取](active-directory-conditional-access.md)。如需有關工作場所中的 Windows 10 裝置及向 Azure AD 註冊後使用者可獲得之體驗的詳細資訊，請參閱[適合企業使用的 Windows 10：使用裝置的工作方式](active-directory-azureadjoin-windows10-devices-overview.md)。


在下列舊版 Windows 中皆支援註冊︰

- Windows 8.1

- Windows 7

針對用來做為桌上型電腦的 Windows Server 電腦使用案例 (例如開發人員使用 Windows Server 做為主要電腦)，可以註冊下列平台︰

- Windows Server 2016

- Windows Server 2012 R2

- Windows Server 2012

- Windows Server 2008 R2

下面將讓您了解您必須在環境中執行什麼動作，才能向貴組織中的 Azure AD 註冊已加入網域的 Windows 裝置。您將會看到：

1. 必要條件

2. 部署和導入

3. 疑難排解

4. 常見問題集

 

## 必要條件 

若要讓已加入網域的裝置能夠自動向 Azure AD 註冊，主要的必要條件就是擁有最新版的 Azure AD Connect。

依據您部署 Azure AD Connect 的方式，不論是快速安裝、自訂安裝還是就地升級，可能都已自動設定下列必要條件：

1. 內部部署 Active Directory 中的「服務連接點」(SCP)，此連接點可供要向 Azure AD 註冊的電腦探索 Azure AD 租用戶資訊。

2. 用於在註冊時進行電腦驗證的「AD FS 發行轉換規則」(適用於同盟組態)。

如果貴組織中有已加入網域的非 Windows 10 電腦，則您需要注意下列事項︰

1. 確定在 Azure AD 中已啟用允許使用者註冊裝置的原則。

2. 確定在 AD FS 中已將 Windows 整合式驗證 (WIA) 設定為多重要素驗證 (MFA) 的有效替代方式。

 

## 用於探索 Azure AD 租用戶的服務連接點 

SCP 物件必須存在於電腦已加入之網域樹系的「組態命名內容」分割區中，此物件含有與電腦將在該處註冊的 Azure AD 租用戶相關的探索資訊。在多樹系 Active Directory 組態中，SCP 必須存在於電腦已加入的所有樹系中。

您應該可以在 Active Directory 中的下列位置找到 SCP：

	CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context] 

> [AZURE.NOTE] 如果樹系的 Active Directory 網域名稱為 example.com，則「組態命名內容」會是 CN=Configuration,DC=example,DC=com

您可以使用下列 PowerShell 指令碼來檢查該物件是否存在，以及用於探索 Azure AD 租用戶的值 (請將範例中的「組態命名內容」取代成您自己的內容)：

	$scp = New-Object System.DirectoryServices.DirectoryEntry; 

	$scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com"; 

	$scp.Keywords; 

$scp.Keywords 的輸出會顯示類似以下的 Azure AD 租用戶資訊：

	azureADName:microsoft.com 

	azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47 

如果 SCP 不存在，您可以在 Azure AD Connect 伺服器上使用下列 PowerShell 指令碼來建立它：

	Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1"; 

	$aadAdminCred = Get-Credential; 

	Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred; 


> [AZURE.NOTE] 執行 $aadAdminCred = Get-Credential 時，請針對在 Get-Credential 快顯視窗出現時輸入的認證使用者名稱使用 user@example.com 格式。執行 Initialize-ADSyncDomainJoinedComputerSync Cmdlet 時，請以用來做為 Active Directory 連接器帳戶的網域帳戶取代 [connector account name]。上述 Cmdlet 會使用倚賴網域控制站 (DC) 中「Active Directory Web 服務」(ADWS) 的 Active Directory PowerShell 模組。在 Windows Server 2008 R2 或更新版本的 DC 中都支援 ADWS。如果您只有 Windows Server 2008 或更舊版的 DC，您可以透過 PowerShell 使用 System.DirectoryServices API 來建立 SCP，然後指派適當的 Keywords 值。

## 用於進行立即電腦註冊的 AD FS 規則 (同盟組織) 

在 Azure AD 的同盟設定上，電腦會依賴 AD FS (或內部部署同盟伺服器) 來向 Azure AD 進行驗證，以在「Azure 裝置註冊服務」(Azure DRS) 註冊。

> [AZURE.NOTE] 在非同盟組態 (亦即同步至 Azure AD 的使用者密碼雜湊) 上，已加入網域的 Windows 10 和 Windows Server 2016 電腦會使用它們寫入至其內部部署電腦帳戶的認證 (此認證會接著透過 Azure AD Connect 被帶到 Azure AD) 向 Azure DRS 進行驗證。針對非同盟組態上的非 Windows 10/Windows Server 2016 電腦，請參閱本文件中下方＜部署和導入＞底下適用於註冊已加入網域之非 Windows 10/Windows Server 2016 電腦的 Windows Installer 套件一節，以了解有哪些選項可供您在貴組織中啟用裝置型 CA。

針對 Windows 10 和 Windows Server 2016 電腦，Azure AD Connect 會將 Azure AD 中的裝置物件與內部部署的電腦帳戶物件建立關聯。若要讓此做法能夠運作，驗證時必須有下列宣告，Azure DRS 才能完成註冊並在一開始就建立裝置物件︰

- `http://schemas.microsoft.com/ws/2012/01/accounttype` - 包含 “DJ” 這個值，用來識別以已加入網域的電腦進行驗證的主體。
- `http://schemas.microsoft.com/identity/claims/onpremobjectguid` - 包含內部部署電腦帳戶的 objectGUID 屬性值。
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` - 包含與內部部署電腦帳戶的 objectSid 屬性值對應的電腦主要 SID。
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` - 包含可讓 Azure AD 信任從 AD FS 或內部部署 STS 簽發之權杖的適當值。在多樹系 Active Directory 組態中，由於電腦所加入的樹系可能與連接至 AD FS 或內部部署 STS 所在之 Azure AD 的樹系不同，因此這相當重要。就 AD FS 案例而言，此值應該是 `http://<domain-name>/adfs/services/trust/`，其中 “<domain-name>” 是 Azure AD 中已驗證的網域名稱。

若要手動建立這些規則，您可以在 AD FS 中，於與您伺服器連線的工作階段上使用下列 PowerShell 指令碼。請注意，必須以貴組織 Azure AD 中已驗證的網域名稱取代第一行。

> [AZURE.NOTE] 如果您沒有以 AD FS 做為您的內部部署同盟伺服器，請依照廠商的指示來建立適當的規則以發出這些宣告。

	$validatedDomain = "example.com"      # Replace example.com with your validated domain name in Azure AD 

	$rule1 = '@RuleName = "Issue object GUID" 

		c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      	=> issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

	$rule2 = '@RuleName = "Issue account type for domain joined computers" 

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

	$rule3 = '@RuleName = "Pass through primary SID" 

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(claim = c2);' 

	$rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account" 

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

	$rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

	$rule6 = '@RuleName = "Update issuer for DJ computer auth" 

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://$validatedDomain/adfs/services/trust/");' 

	$existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

	$updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6 

	$crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 
 
	Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

> [AZURE.NOTE] 已加入網域的 Windows 10 和 Windows Server 2016 電腦將會使用 Windows 整合式驗證，向 AD FS 所裝載的作用中 WS-Trust 端點進行驗證。請確定此端點已啟用。如果您使用 Web 驗證 Proxy，也需確定已透過 Proxy 發佈此端點。端點是 adfs/services/trust/13/windowstransport。它應該在 AD FS 管理主控台的 [服務] > [端點] 底下顯示為已啟用。如果您沒有以 AD FS 做為您的內部部署同盟伺服器，請依照廠商的指示來確定已啟用對應的端點。

 

## 確定已設定 AD FS 來支援驗證要註冊的裝置

您必須確定在 AD FS 中，已針對裝置註冊將 Windows 整合式驗證 (WIA) 設定為多重要素驗證 (MFA) 的有效替代方式。

為此，您必須擁有一個會傳遞驗證方法的發行轉換規則。

1. 開啟 AD FS 管理主控台，然後瀏覽至 [AD FS] > [信任關係] > [信賴憑證者信任]。

2. 在 [Microsoft Office 365 身分識別平台] 信賴憑證者信任物件上按一下滑鼠右鍵，然後選取 [編輯宣告規則]。

2.	在 [發佈轉換規則] 索引標籤上，選取 [新增規則]。

3.	從 [宣告規則] 範本清單中，選取 [使用自訂規則傳送宣告]。

4.	選取 [下一步]。

4.	在 [宣告規則名稱] 文字方塊中，輸入「驗證方法宣告規則」。

5.	在 [宣告規則] 文字方塊中，輸入 `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
=> issue(claim = c);`

6. 在您的同盟伺服器上，開啟 Windows PowerShell。

7. 輸入以下命令：

	`Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**<RPObjectName>** 是 Azure Active Directory 信賴憑證者信任物件的信賴憑證者物件名稱。此物件通常名為「Microsoft Office 365 身分識別平台」。

 

## 部署和導入 

完成必要條件之後，已加入網域的電腦便可以向 Azure AD 註冊。

已加入網域的「Windows 10 年度更新版」和 Windows Server 2016 電腦會在下次重新開機或使用者登入 Windows 時，自動向 Azure AD 註冊。加入網域的新電腦會在進行加入網域作業之後，於重新開機時向 Azure AD 註冊。

> [AZURE.NOTE] 已加入網域的 Windows 10 電腦只有在已設定導入「群組原則物件」的情況下，才會自動向 Azure AD 註冊。如需詳細資訊，請參閱下一節。

若要控制已加入網域之 Windows 10/Windows Server 2016 電腦的自動註冊導入，有一個可供您針對該目的使用的「群組原則」物件。若要導入已加入網域之非 Windows 10 電腦的自動註冊，有一個可供您部署到所選電腦的 Windows Installer 套件。

> [AZURE.NOTE] 用於控制導入的「群組原則」也會觸發已加入網域之 Windows 8.1 電腦的註冊。您也可以選擇使用此原則來註冊已加入網域的 Windows 8.1 電腦，或者如果您有混合的 Windows 版本 (包括 7 或 Windows Server 版本)，則可以選擇使用 Windows Installer 套件來啟用所有非 Windows 10/Windows Server 2016 電腦的註冊。

### 可控制自動註冊導入的群組原則物件 

若要控制已加入網域之電腦的 Azure AD 自動註冊導入，您可以將群組原則「註冊加入網域的電腦為裝置」部署到您想要註冊的電腦。例如，您可以根據安全性群組來部署原則，或是將原則部署到組織單位 (OU)。

若要設定原則，請執行下列步驟：

1. 開啟 [伺服器管理員]，然後瀏覽至 [工具] > [群組原則管理]。

2. 從 [群組原則管理]，瀏覽至與您想要在其中啟用 Windows 10 或 Windows Server 2016 電腦自動註冊的網域對應的網域節點。

3. 在 [群組原則物件] 上按一下滑鼠右鍵，然後選取 [新增]。

4. 輸入您的群組原則物件名稱，例如「自動向 Azure AD 註冊」。按一下 [確定]。

4. 在新群組原則物件上按一下滑鼠右鍵，然後選取 [編輯]。

5. 瀏覽至 [電腦設定] > [原則] > [系統管理範本] > [Windows 元件] > [裝置註冊]，在 [註冊加入網域的電腦為裝置] 上按一下滑鼠右鍵，然後選取 [編輯]。

	> [AZURE.NOTE] 此「群組原則」範本已從舊版 [群組原則管理] 主控台重新命名。如果您執行的是舊版主控台，此原則將會在 [電腦設定] > [原則] > [系統管理範本] > [Windows 元件] > 名稱為 [自動將用戶端電腦加入工作場所] 的 [加入工作場所] 底下。

6. 選取 [已啟用] 選項按鈕，然後按一下 [套用]。

7. 按一下 [確定]。

7. 將群組原則物件連結到您選擇的位置。例如，電腦所在的特定組織單位 (OU)，或包含將自動向 Azure AD 註冊之電腦的特定安全性群組。若要為貴組織中所有已加入網域的 Windows 10 和 Windows Server 2016 電腦啟用此原則，請將「群組原則」物件連結至網域。

## 適用於非 Windows 10 電腦的 MSI 套件  

若要註冊執行 Windows 7、Windows 8.1、Windows Server 2008 R2、Windows Server 2012 或 Windows Server 2012 R2 的已加入網域電腦，有一個 Windows Installer 套件 (.msi) 可供您下載：

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

您應該使用軟體發佈系統 (例如 System Center Configuration Manager) 來部署此套件。此套件可使用 /quiet 參數來支援標準的無訊息安裝選項。如果您使用 System Center Configuration Manager 2016，您將享有額外的好處，例如能夠追蹤已完成的註冊。如需詳細資訊，請參閱 [System Center 2016](https://www.microsoft.com/cloud-platform/system-center-2016)。

安裝程式會在系統上建立一個「排定的工作」，此工作是在使用者的情境脈絡中執行，並於使用者登入 Windows 時觸發。此工作會在使用 Windows 整合式驗證進行驗證之後，使用使用者認證以無訊息方式向 Azure AD 註冊裝置。您可以在 [Microsoft] > [加入工作場所] 底下的 [工作排程器程式庫] 中，找到這個「排定的工作」。



## 其他主題

- [Azure Active Directory 條件式存取](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0928_2016-->