---
title: 設定 Azure 執行身分帳戶 | Microsoft Docs
description: 引導您在 Azure 自動化中建立、測試和舉例使用安全性主體驗證的逐步解說教學課程。
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
keywords: 服務主體名稱, setspn, azure 驗證

ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: magoedte

---
# 使用 Azure 執行身分帳戶驗證 Runbook
本主題將示範如何從 Azure 入口網站使用執行身分帳戶功能設定自動化帳戶，以驗證在 Azure Resource Manager 或 Azure 服務管理中管理資源的 Runbook。

當您在 Azure 入口網站中建立新的自動化帳戶時，它會自動建立：

* 執行身分帳戶，以在 Azure Active Directory 中建立新的服務主體、憑證，以及指派參與者角色型存取控制 (RBAC)，其將使用 Runbook 管理 Resource Manager 資源。
* 傳統執行身分帳戶 (藉由上傳管理憑證)，其將使用 Runbook 管理 Azure 服務管理或傳統資源。

這個帳戶可為您簡化程序，協助您快速開始建置和部署 Runbook 以支援您的自動化需求。

使用執行身分和傳統執行身分帳戶，您可以︰

* 在 Azure 入口網站中透過 Runbook 管理 Azure Resource Manager 或 Azure 服務管理資源時，提供標準化的 Azure 驗證方式
* 自動使用 Azure 警示中設定的全域 Runbook。

> [!NOTE]
> 若要搭配自動化全域 Runbook 使用 Azure [警示整合功能](../azure-portal/insights-receive-alert-notifications.md)，您需要具備使用執行身分和傳統執行身分帳戶所設定的自動化帳戶。您可以選取已定義執行身分和傳統執行身分帳戶的自動化帳戶，或選擇建立新的自動化帳戶。
> 
> 

我們將示範如何從 Azure 入口網站建立自動化帳戶、如何使用 PowerShell 更新自動化帳戶，以及如何在 Runbook 中進行驗證。

在我們這麼做之前，您應該先了解並考量一些事項，然後再繼續。

1. 這不會影響傳統或 Resource Manager 部署模型中已建立的現有自動化帳戶。
2. 這只適用於透過 Azure 入口網站建立的自動化帳戶。嘗試從傳統入口網站建立帳戶，並不會複寫執行身分帳戶組態。
3. 如果您目前以先前建立的 Runbook 和資產 (也就是排程、變數等等) 來管理傳統資源，而且您希望這些 Runbook 使用新的傳統執行身分帳戶進行驗證，您必須將它們移轉到新的自動化帳戶，或使用下面的 PowerShell 指令碼更新您現有的帳戶。
4. 若要使用新的執行身分帳戶和傳統執行身分自動化帳戶進行驗證，您必須以下列範例程式碼修改現有的 Runbook。**請注意**，執行身分帳戶適用於使用以憑證為基礎的服務主體驗證 Resource Manager 資源，而傳統執行身分帳戶適用於使用管理憑證驗證服務管理資源。

## 從 Azure 入口網站建立新的自動化帳戶
在本節中，您將執行下列步驟以從 Azure 入口網站建立新的 Azure 自動化帳戶。這會建立執行身分和傳統執行身分帳戶。

> [!NOTE]
> 執行這些步驟的使用者「必須」是訂閱管理員角色的成員，而且是授與使用者存取訂用帳戶權限的共同管理員。此使用者也必須新增為該訂用帳戶預設 Active Directory 的使用者；此帳戶不需要指派給特殊權限的角色。
> 
> 

1. 您必須以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
2. 選取 [自動化帳戶]。
3. 在 [自動化帳戶] 刀鋒視窗中，按一下 [新增]。<br>![新增自動化帳戶](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > 如果您在 [新增自動化帳戶] 刀鋒視窗中看到下列警告，這是因為您的帳戶不是訂用帳戶管理員角色的成員和訂用帳戶的共同管理員。<br>![新增自動化帳戶警告](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. 在 [新增自動化帳戶] 刀鋒視窗的 [名稱] 方塊中，輸入新的自動化帳戶的名稱。
5. 如果您有多個訂用帳戶，請為新的自動化帳戶指定其中一個訂用帳戶，並指定新的或現有的 [資源群組] 和 Azure 資料中心的 [位置]。
6. 確認已為 [建立 Azure 執行身分帳戶] 選項選取 [是] 這個值，然後按一下 [建立] 按鈕。
   
   > [!NOTE]
   > 如果您選取選項 [否] 以選擇不要建立執行身分帳戶，則會在 [新增自動化帳戶] 刀鋒視窗中看到一則警告訊息。雖然此帳戶建立於 Azure 入口網站中，但在傳統或 Resource Manager 訂用帳戶目錄服務內不會有對應的驗證身分識別，因此無法存取您訂用帳戶中的資源。這將導致參考此帳戶的任何 Runbook 無法進行驗證並對這些部署模型中的資源執行工作。
   > 
   > ![新增自動化帳戶警告](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br> 若未建立服務主體，將不會指派參與者角色。
   > 
   > 
7. 在 Azure 建立自動化帳戶時，您可以在功能表的 [通知] 底下追蹤進度。

### 包含的資源
順利建立自動化帳戶時，系統會自動為您建立幾項資源。下表摘要說明執行身分帳戶的資源。<br>

| 資源 | 說明 |
| --- | --- |
| AzureAutomationTutorial Runbook |此為 PowerShell Runbook 範例，示範如何使用執行身分帳戶進行驗證以及取得 Resource Manager 資源。 |
| AzureAutomationTutorialScript Runbook |此為 PowerShell Runbook 範例，示範如何使用執行身分帳戶進行驗證以及取得 Resource Manager 資源。 |
| AzureRunAsCertificate |在建立自動化帳戶期間自動建立，或使用下列適用於現有帳戶的 PowerShell 指令碼建立的憑證資產。它可讓您向 Azure 進行驗證，以便從 Runbook 管理 Azure Resource Manager 資源。此憑證有一年的有效期。 |
| AzureRunAsConnection |在建立自動化帳戶期間自動建立，或使用下列適用於現有帳戶的 PowerShell 指令碼建立的連線資產。 |

下表摘要說明傳統執行身分帳戶的資源。<br>

| 資源 | 說明 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |此範例 Runbook 可使用傳統執行身分帳戶 (憑證) 取得訂用帳戶中的所有傳統 VM，然後輸出 VM 名稱和狀態。 |
| AzureClassicAutomationTutorial 指令碼 Runbook |此範例 Runbook 可使用傳統執行身分帳戶 (憑證) 取得訂用帳戶中的所有傳統 VM，然後輸出 VM 名稱和狀態。 |
| AzureClassicRunAsCertificate |自動建立的憑證資產，其用來向 Azure 進行驗證，以便從 Runbook 管理 Azure 傳統資源。此憑證有一年的有效期。 |
| AzureClassicRunAsConnection |自動建立的連線資產，其用來向 Azure 進行驗證，以便從 Runbook 管理 Azure 傳統資源。 |

## 確認執行身分驗證
接下來我們將執行一項小測試，以確認您可以使用新的執行身分帳戶成功進行驗證。

1. 在 Azure 入口網站中，開啟先前建立的自動化帳戶。
2. 按一下 [Runbook] 磚以開啟 Runbook 的清單。
3. 選取 **AzureAutomationTutorialScript** Runbook，然後按一下 [啟動] 來啟動 Runbook。您會收到提示，確認您想要啟動 Runbook。
4. 已建立 [Runbook 作業](automation-runbook-execution.md)、顯示 [作業] 刀鋒視窗，而作業狀態會顯示在 [作業摘要] 圖格中。
5. 作業狀態一開始會顯示為「已排入佇列」，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。然後當背景工作宣告該工作時，狀態將變更為*正在開始*，然後 Runbook 實際開始執行時再變更為*執行中*。
6. Runbook 作業完成時，我們應該會看到 [完成] 狀態。<br> ![安全性主體 Runbook 測試](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. 若要查看 Runbook 的詳細結果，請按一下 [輸出] 圖格。
8. 在 [輸出] 刀鋒視窗中，您應該會看到它已成功驗證並傳回資源群組中的所有可用資源清單。
9. 關閉 [輸出] 刀鋒視窗以返回 [作業摘要] 刀鋒視窗。
10. 關閉 [作業摘要] 和對應的 **AzureAutomationTutorialScript** Runbook 刀鋒視窗。

## 確認傳統執行身分驗證
接下來我們將執行一項小測試，以確認您可以使用新的傳統執行身分帳戶成功進行驗證。

1. 在 Azure 入口網站中，開啟先前建立的自動化帳戶。
2. 按一下 [Runbook] 磚以開啟 Runbook 的清單。
3. 選取 **AzureClassicAutomationTutorialScript** Runbook，然後按一下 [啟動] 來啟動 Runbook。您會收到提示，確認您想要啟動 Runbook。
4. 已建立 [Runbook 作業](automation-runbook-execution.md)、顯示 [作業] 刀鋒視窗，而作業狀態會顯示在 [作業摘要] 圖格中。
5. 作業狀態一開始會顯示為「已排入佇列」，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。然後當背景工作宣告該工作時，狀態將變更為*正在開始*，然後 Runbook 實際開始執行時再變更為*執行中*。
6. Runbook 作業完成時，我們應該會看到 [完成] 狀態。<br> ![安全性主體 Runbook 測試](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. 若要查看 Runbook 的詳細結果，請按一下 [輸出] 圖格。
8. 在 [輸出] 刀鋒視窗中，您應該會看到它已成功驗證並傳回訂用帳戶中的所有傳統 VM 清單。
9. 關閉 [輸出] 刀鋒視窗以返回 [作業摘要] 刀鋒視窗。
10. 關閉 [作業摘要] 和對應的 **AzureClassicAutomationTutorialScript** Runbook 刀鋒視窗。

## 使用 PowerShell 更新自動化帳戶
我們在此為您提供使用 PowerShell 來更新現有自動化帳戶的選項，但前提是︰

1. 您已建立一個自動化帳戶，但拒絕建立執行身分帳戶
2. 您已經有自動化帳戶可管理 Resource Manager 資源，而且您想要加以更新以包含可供 Runbook 驗證的執行身分帳戶
3. 您已經有自動化帳戶可管理傳統資源，而且您想要加以更新以使用傳統執行身分，而不是建立新的帳戶並將 Runbook 和資產移轉至該帳戶

在繼續之前，請確認下列事項︰

1. 您已下載並安裝 [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) (如果您執行 Windows 7)。如果您執行的是 Windows Server 2012 R2、Windows Server 2012、Windows 2008 R2、Windows 8.1 和 Windows 7 SP1，則 [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) 可供安裝。
2. Azure PowerShell 1.0。如需有關此版本以及如何安裝的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。
3. 您已建立自動化帳戶。此帳戶會被參照做為以下兩個指令碼所含參數 (–AutomationAccountName 和 -ApplicationDisplayName) 的值。

若要在 Azure 入口網站中取得 SubscriptionID、ResourceGroup 和 AutomationAccountName 的值 (這些都是指令碼的必要參數)，請從 [自動化帳戶] 刀鋒視窗選取您的自動化帳戶，然後選取 [所有設定]。在 [所有設定] 刀鋒視窗中，選取 [帳戶設定] 之下的 [屬性]。在 [屬性] 刀鋒視窗中，您可以記下這些值。<br> ![自動化帳戶屬性](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)

### 建立執行身分帳戶 PowerShell 指令碼
下面的 PowerShell 指令碼會設定下列項目︰

* Azure AD 應用程式，其可使用自我簽署憑證進行驗證、建立此應用程式在 Azure AD 中的服務主體帳戶，並在目前的訂用帳戶中為此帳戶指派參與者角色 (您可以將此角色變更為擁有者或任何其他角色)。如需進一步資訊，請檢閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)文章。
* 名為 **AzureRunAsCertificate** 的指定自動化帳戶中的自動化憑證資產，其會保存服務主體所使用的憑證。
* 指定的自動化帳戶中名為 **AzureRunAsConnection** 的自動化連線資產，其保有 applicationId、tenantId、subscriptionId 和憑證指紋。

下列步驟將逐步引導您進行執行指令碼的程序。

1. 將下列指令碼儲存到電腦。在此範例中，請以檔案名稱 **New-AzureServicePrincipal.ps1** 進行儲存。
   
        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,
   
        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,
   
        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,
   
        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,
   
        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )
   
        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
   
        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())
   
        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue
   
        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential
   
        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose
   
        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        } 
   
        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
   
        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
2. 在電腦上以提高的使用者權限從 [開始] 畫面啟動 **Windows PowerShell**。
3. 從提高權限的 PowerShell 命令列殼層，瀏覽至包含步驟 1 所建立指令碼的資料夾，並執行指令碼變更 –ResourceGroup、-AutomationAccountName、-ApplicationDisplayName、-SubscriptionId 和 -CertPlainPassword 參數的值。<br>
   
   > [!NOTE]
   > 執行指令碼之後，您會收到向 Azure 進行驗證的提示。您必須以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。
   > 
   > 
   
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
   <br>

順利完成指令碼之後，請參考下面的[範例程式碼](#sample-code-to-authenticate-with-resource-manager-resources)使用 Resource Manager 資源進行驗證並驗證認證組態。

### 建立傳統執行身分帳戶 PowerShell 指令碼
下面的 PowerShell 指令碼會設定下列項目︰

* 指定的自動化帳戶中名為 **AzureClassicRunAsCertificate** 的自動化憑證資產，其會保存用來驗證 Runbook 的憑證。
* 指定的自動化帳戶中名為 **AzureClassicRunAsConnection** 的自動化連線資產，其會保存訂用帳戶名稱、applicationId 和憑證資產名稱。

此指令碼會建立自我簽署的管理憑證，並將它儲存到您的電腦上用來執行 PowerShell 工作階段的使用者設定檔之下的暫存檔案資料夾中 - %USERPROFILE%\\AppData\\Local\\Temp。在指令碼執行之後，您必須將 Azure 管理憑證上傳至自動化帳戶建立所在的訂用帳戶的管理存放區中。下列步驟將逐步引導您進行執行指令碼和上傳憑證的程序。

1. 將下列指令碼儲存到電腦。在此範例中，請以檔案名稱 **New-AzureClassicRunAsAccount.ps1** 進行儲存。
   
        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,
   
        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,
   
        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,
   
        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,
   
        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )
   
        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose
   
        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues 
   
        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"
2. 在電腦上以提高的使用者權限從 [開始] 畫面啟動 **Windows PowerShell**。
3. 從提高權限的 PowerShell 命令列殼層，瀏覽至包含步驟 1 所建立指令碼的資料夾，並執行指令碼變更 –ResourceGroup、-AutomationAccountName、-ApplicationDisplayName、-SubscriptionId 和 -CertPlainPassword 參數的值。<br>
   
   > [!NOTE]
   > 執行指令碼之後，您會收到向 Azure 進行驗證的提示。您必須以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。
   > 
   > 
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>" 

順利完成指令碼之後，您必須複製使用者設定檔 **Temp** 資料夾中建立的憑證。請遵循[上傳管理 API 憑證](../azure-api-management-certs.md)至 Azure 傳統入口網站的步驟，然後參考[範例程式碼](#sample-code-to-authenticate-with-service-management-resources)來驗證認證組態與服務管理資源。

## 用來向 Resource Manager 資源進行驗證的範例程式碼
您可以使用下列已更新的範例程式碼 (取自 **AzureAutomationTutorialScript** 範例 Runbook)，以執行身分帳戶進行驗證來使用 Runbook 管理 Resource Manager 資源。

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Logging in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    } 


指令碼中包含兩行額外的程式碼以支援參考訂用帳戶內容，以便您可以輕鬆地在多個訂用帳戶之間進行工作。名為 SubscriptionId 的變數資產包含訂用帳戶識別碼，而且在 Add-AzureRmAccount Cmdlet 陳述式之後，[Set-AzureRmContext Cmdlet](https://msdn.microsoft.com/library/mt619263.aspx) 會以參數集 -SubscriptionId 開頭。如果變數名稱太過一般，您可以修改變數名稱使其包含前置詞或其他命名慣例，以便讓名稱能夠更容易地指出您的目的。或者，您可以使用參數集 -SubscriptionName (而非 -SubscriptionId) 與對應的變數資產。

請注意，Runbook 中用來驗證的 Cmdlet (**Add-AzureRmAccount**) 會使用 ServicePrincipalCertificate 參數集。它藉由使用服務主體憑證 (而非認證) 進行驗證。

## 用來向服務管理資源進行驗證的範例程式碼
您可以使用下列已更新的範例程式碼 (取自 **AzureClassicAutomationTutorialScript** 範例 Runbook)，使用傳統執行身分帳戶進行驗證以使用 Runbook 管理傳統資源。

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert 
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID


## 後續步驟
* 如需服務主體的詳細資訊，請參閱[應用程式物件和服務主體物件](../active-directory/active-directory-application-objects.md)。
* 如需 Azure 自動化中角色型存取控制的詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
* 如需有關憑證和 Azure 服務的詳細資訊，請參考 [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)。

<!---HONumber=AcomDC_1005_2016-->