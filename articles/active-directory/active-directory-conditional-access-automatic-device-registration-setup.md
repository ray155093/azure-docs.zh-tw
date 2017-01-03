---
title: "如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊 | Microsoft Docs"
description: "設定讓已加入網域的 Windows 裝置以自動和無訊息方式向 Azure Active Directory 註冊。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f26ca6e30e3070b2d2ee1861b88c52013158ba03
ms.openlocfilehash: 45f52d4e02896d38726552adbe81261551151683


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊

若要使用 [Azure Active Directory 裝置型條件式存取](active-directory-conditional-access.md)，電腦必須向 Azure Active Directory (Azure AD) 註冊。 本文會提供可供設定讓已加入網域的 Windows 裝置自動向組織中之 Azure AD 註冊的相關步驟。

> [!NOTE]
> 「Windows 10 11 月更新版」會提供一些增強的 Azure AD 使用者體驗，不過「Windows 10 年度更新版」才會完整支援裝置型條件式存取。 如需有關條件式存取的詳細資訊，請參閱 [Azure Active Directory 裝置型條件式存取](active-directory-conditional-access.md)。 如需有關工作場所中的 Windows 10 裝置以及使用者如何向 Azure AD 註冊 Windows 10 裝置的詳細資訊，請參閱[適合企業使用的 Windows 10：使用裝置工作](active-directory-azureadjoin-windows10-devices-overview.md)。
> 
> 

對於執行 Windows 的裝置，您可以註冊某些舊版的 Windows，包括︰

- Windows 8.1
- Windows 7

對於執行 Windows Server 的裝置，您可以註冊下列平台︰

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>必要條件

若要讓已加入網域的裝置自動註冊，主要的需求就是擁有最新版的 Azure Active Directory Connect (Azure AD Connect)。

依據您部署 Azure AD Connect 的方式，不論是使用快速安裝、自訂安裝還是就地升級，可能都已自動設定下列必要條件：

- **內部部署 Active Directory 中的服務連接點** - 可供要向 Azure AD 註冊的電腦探索 Azure AD 租用戶資訊。
 
- **Active Directory Federation Services (AD FS) 發佈轉換規則** - 用於在註冊時進行電腦驗證 (適用於同盟組態)。

如果您的組織中有些裝置不是已加入網域的 Windows 10 裝置，請執行下列步驟︰

* 在 Azure AD 中設定原則，讓使用者可以註冊裝置
* 將 Windows 整合式驗證 (WIA) 設定為 AD FS 中 Multi-Factor Authentication 的有效替代方式

## <a name="step-1-configure-service-connection-point"></a>步驟 1︰設定服務連接點 

服務連接點 (SCP) 物件必須存在於電腦網域的組態命名內容分割區中。 服務連接點含有電腦註冊所在 Azure AD 租用戶的相關探索資訊。 在多樹系 Active Directory 組態中，服務連接點必須存在於具有已加入網域電腦的所有樹系中。

SCP 位於︰  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[您的組態命名內容]**

如果樹系的 Active Directory 網域名稱為 *example.com*，則組態命名內容為：  

**CN=Configuration,DC=example,DC=com**

透過下列 Windows PowerShell 指令碼，您可以確認物件是否存在，並擷取探索值︰ 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

**$scp.Keywords** 的輸出會顯示 Azure AD 租用戶資訊，例如：

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

如果服務連接點不存在，請在 Azure AD Connect 伺服器上執行下列 PowerShell 指令碼加以建立：

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**備註：**

- 當您執行 **$aadAdminCred = Get-Credential** 時，您必須輸入使用者名稱。 使用者名稱請使用下列格式︰**user@example.com** 


- 當您執行 **Initialize-ADSyncDomainJoinedComputerSync** Cmdlet 時，請以 Active Directory 連接器帳戶中使用的網域帳戶取代 [連接器帳戶名稱]。
  
- 此 Cmdlet 會使用 Active Directory PowerShell 模組，其倚賴網域控制站中的 Active Directory Web 服務。 Windows Server 2008 R2 和更新版本的網域控制站可支援 Active Directory Web 服務。 對於 Windows Server 2008 或更早版本的網域控制站，透過 PowerShell 使用 System.DirectoryServices API 來建立服務連接點，然後指派 Keywords 值。
 
 



##<a name="step-2-register-your-devices"></a>步驟 2︰註冊裝置

用於註冊裝置的正確步驟取決於組織是否已同盟。 


### <a name="device-registration-in-non-federated-organizations"></a>非同盟組織的裝置註冊

下列條件成立時才支援非同盟組織的裝置註冊︰

- 您的裝置執行 Windows 10 及 Windows Server 2016
- 您的裝置已加入網域
- 已啟用使用 Azure AD Connect 進行密碼同步處理

如果上述所有需求皆符合，您不需要執行任何動作就能註冊裝置。  


### <a name="device-registration-in-federated-organizations"></a>同盟組織的裝置註冊

在同盟 Azure AD 組態中，裝置倚賴 AD FS (或內部部署同盟伺服器) 向 Azure AD 進行驗證。 裝置會對 Azure Active Directory 裝置註冊服務進行註冊。

對於 Windows 10 和 Windows Server 2016 電腦，Azure AD Connect 會將 Azure AD 中的裝置物件與內部部署的電腦帳戶物件建立關聯。 驗證期間必須有下列宣告，Azure AD 裝置註冊服務才能完成註冊並建立裝置物件︰

- **http://schemas.microsoft.com/ws/2012/01/accounttype** - 包含 DJ 值，該值會將主體驗證器視為已加入網域的電腦。

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** - 包含內部部署電腦帳戶的 **objectGUID** 屬性值。
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** - 包含電腦的主要安全性識別碼 (SID)，其對應於內部部署電腦帳戶的 **objectSid** 屬性值。

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** - 包含 Azure AD 用於信任 AD FS 或內部部署 Security Token Service (STS) 所發出之權杖的值。 如果您的 Azure AD 中有數個已驗證的網域，則這個值很重要。 在 AD FS 案例中，請使用 **http://\<domain-name**\>/adfs/services/trust/**，其中 **\<domain-name\>** 是 Azure AD 中已驗證的網域名稱。

如需已驗證之網域名稱的詳細資料，請參閱[將自訂網域名稱新增至 Azure Active Directory](active-directory-add-domain.md)。  
若要取得已驗證之公司網域的清單，您可以使用 [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain) Cmdlet。 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


已加入網域的 Windows 10 和 Windows Server 2016 電腦會使用 Windows 整合式驗證，向 AD FS 所裝載的作用中 WS-Trust 端點進行驗證。 請確定此端點已啟用。 如果您使用 Web 驗證 Proxy，也需確定已透過 Proxy 發佈此端點。 端點是 **adfs/services/trust/13/windowstransport**。 

它應該會在 AD FS 管理主控台的 [服務] > [端點] 底下啟用。 如果您沒有以 AD FS 做為您的內部部署同盟伺服器，請依照廠商的指示來確定已啟用對應的端點。 



> [!NOTE]
> 如果您未將 AD FS 用於您的內部部署同盟伺服器，請依照廠商的指示來建立規則以發出這些宣告。
> 
> 




**若要在 AD FS 中手動建立規則︰**

- 選取下列其中一個 Windows PowerShell 指令碼 
- 在連線至伺服器的工作階段中執行 Windows PowerShell 指令碼。 
- 以 Azure AD 中已驗證的組織網域名稱取代第一行。




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>在單一網域環境中設定 AD FS 規則

如果您只有**一個已驗證的網域**，請使用下列指令碼新增 AD FS 規則：


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

    $rule1 = '@RuleName = "Issue object GUID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(claim = c2);' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>在多個網域環境中設定 AD FS 規則

如果您有多個已驗證的網域，請執行下列步驟︰

1. 移除 Azure AD Connect 所建立的現有 **IssuerID** 規則。  
此規則的範例如下：c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. 執行此指令碼︰ 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

        $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

        $rule2 = '@RuleName = "Issue account type for domain joined computers" 

        c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

        $rule3 = '@RuleName = "Pass through primary SID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(claim = c2);' 

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>步驟 3︰設定 AD FS 以驗證裝置註冊

確定 WIA 已設定為 Multi-Factor Authentication 的有效替代方式，以便在 AD FS 中進行裝置註冊。 若要這麼做，您必須擁有可傳遞驗證方法的發佈轉換規則。

1. 在 AD FS 管理主控台中，移至 [AD FS]  >  [信任關係] >  [信賴憑證者信任]。
2. 在 [Microsoft Office 365 身分識別平台] 信賴憑證者信任物件上按一下滑鼠右鍵，然後選取 [編輯宣告規則]。
3. 在 [發佈轉換規則] 索引標籤上，選取 [新增規則]。
4. 在 [宣告規則] 範本清單中，選取 [使用自訂規則傳送宣告]。
5. 選取 [下一步] 。
6. 在 [宣告規則名稱] 方塊中，輸入**驗證方法宣告規則**。
7. 在 [宣告規則] 方塊中，輸入此規則︰  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. 在同盟伺服器上，輸入此 PowerShell 命令：
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>** 是 Azure AD 信賴憑證者信任物件的信賴憑證者物件名稱。 此物件通常名為「Microsoft Office 365 身分識別平台」。



##<a name="step-4-deployment-and-rollout"></a>步驟 4︰部署與導入

當已加入網域的電腦符合必要條件時，便可以向 Azure AD 註冊。

已加入網域的「Windows 10 年度更新版」和 Windows Server 2016 電腦會在下次重新啟動裝置或在使用者登入 Windows 時，自動向 Azure AD 註冊。 加入網域的新電腦會在加入網域作業之後，於裝置重新啟動時向 Azure AD 註冊。

> [!NOTE]
> 已加入網域的 Windows 10 電腦只有在已設定導入「群組原則」物件的情況下，才會自動向 Azure AD 註冊。
> 
> 

您可以使用「群組原則」物件來控制已加入網域之 Windows 10 和 Windows Server 2016 電腦的自動註冊導入。 

若要導入已加入網域之非 Windows 10 電腦的自動註冊，您可以將 Windows Installer 套件部署到您所選的電腦。

> [!NOTE]
> 用於控制導入的「群組原則」也會觸發已加入網域之 Windows 8.1 電腦的註冊。 您可以使用此原則來註冊已加入網域的 Windows 8.1 電腦。 或者，如果您有混合的 Windows 版本 (包括 Windows 7 或 Windows Server 版本)，您可以使用 Windows Installer 套件來註冊所有非 Windows 10 和 Windows Server 2016 電腦。
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>建立可控制自動註冊導入的群組原則物件

若要控制已加入網域之電腦的 Azure AD 自動註冊導入，您可以將 [將已加入網域的電腦註冊為裝置] 群組原則部署到您要註冊的電腦。 例如，您可以將此原則部署到組織單位或安全性群組。

**若要設定原則︰**

1. 開啟 [伺服器管理員]，然後移至 [工具]  >  [群組原則管理]。
2. 移至您要啟用自動註冊 Windows 10 或 Windows Server 2016 電腦的網域所對應的網域節點。
3. 在 [群組原則物件] 上按一下滑鼠右鍵，然後選取 [新增]。
4. 輸入群組原則物件的名稱。 例如，「自動向 Azure AD 註冊」。 選取 [確定] 。
5. 以滑鼠右鍵按一下新的群組原則物件，然後選取 [編輯]。
6. 移至 [電腦設定]  >  [原則]  >  [系統管理範本]  >  [Windows 元件]  >  [裝置註冊]。 以滑鼠右鍵按一下 [將已加入網域的電腦註冊為裝置]，然後選取 [編輯]。
   
   > [!NOTE]
   > 此「群組原則」範本已從舊版 [群組原則管理] 主控台重新命名。 如果您使用舊版的主控台，請移至 [電腦設定]  >  [原則]  >  [系統管理範本]  >  [Windows 元件]  >  [工作場所聯結]  >  [自動將用戶端電腦聯結工作場所]。
   > 
   > 
7. 選取 [已啟用]，然後選取 [套用]。
8. 選取 [確定] 。
9. 將群組原則物件連結到您選擇的位置。 例如，您可以將它連結到特定組織單位。 也可以將它連結到會向 Azure AD 自動註冊的特定電腦安全性群組。 若要為貴組織中所有已加入網域的 Windows 10 和 Windows Server 2016 電腦設定此原則，請將「群組原則」物件連結至網域。

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>非 Windows 10 電腦的 Windows Installer 套件
若要在同盟環境中註冊執行 Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 的已加入網域電腦，您可以下載並安裝下列 Windows Installer 套件 (.msi) 檔案：

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

使用軟體發佈系統 (例如 System Center Configuration Manager) 來部署此套件。 此套件使用 quiet 參數來支援標準的無訊息安裝選項。 System Center Configuration Manager 2016 提供額外的舊版好處，例如能夠追蹤已完成的註冊。 如需詳細資訊，請參閱 [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center)。

安裝程式會在系統上建立排定的工作，此工作是在使用者的內容中執行。 此工作會在使用者登入 Windows 時觸發。 此工作會在透過 IWA 驗證之後，利用使用者認證以無訊息方式向 Azure AD 註冊裝置。 若要查看排定的工作，請移至 [Microsoft]  >  [工作場所聯結]，然後移至工作排程器程式庫。

## <a name="next-steps"></a>後續步驟
* [Azure Active Directory 條件式存取](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO4-->


