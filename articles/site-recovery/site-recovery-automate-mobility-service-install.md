<properties
	pageTitle="使用 Site Recovery 搭配 Azure Automation DSC 將 VMWare 虛擬機器複寫至 Azure | Microsoft Azure"
	description="說明如何使用 Azure Automation DSC 將 ASR 行動服務和虛擬/實體機器的 Azure 代理程式自動部署至 Azure 。"
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# 使用 Site Recovery 搭配 Azure Automation DSC 將 VMware VM 複寫至 Azure

在 OMS 中，我們提供完整的備份和災害復原解決方案，以便做為您的商務持續性計畫的一部分。

我們已從使用 Hyper-V 複本的 Hyper-V 著手進行，但已擴大支援異質性安裝程式，因為我們清楚的知道客戶在其雲端中擁有多個 Hypervisor 與平台。

如果您目前正在執行 VMware 工作負載或/和實體伺服器，我們會依賴執行您環境中執行之所有 Site Recovery 元件的管理伺服器，處理當 Azure 是目的地時與 Azure 的通訊和資料複寫作業。

## 使用 OMS Automation DSC 部署 ASR 行動服務
讓我們從快速分析此管理伺服器的實際作用著手︰

管理伺服器可執行數個伺服器角色，例如**組態** – 該角色可協調通訊及管理資料複寫與復原程序。

此外，**處理序**角色可做為複寫閘道，它會從受保護的來源機器接收複寫資料，將複寫資料傳送至 Azure 儲存體帳戶前，以快取、壓縮和加密進行最佳化。處理序角色的功能之一就是它還會將行動服務的安裝推送至受保護的機器，並執行 VMWare VM 的自動探索。

如果從 Azure 容錯回復，**主要目標**角色會負起責任，並在這項作業中處理複寫資料。

對於受保護的機器，我們會依賴**行動服務** – 此元件會部署至您要複寫至 Azure 的每部機器 (VMware VM 或實體伺服器)。其角色是擷取在電腦上寫入的資料，並將這些資料轉送到管理伺服器 (處理序角色)。

處理商務持續性時，請務必了解您的工作負載、您的基礎結構，以及為了符合您的 RTO 和 RPO 需求所牽涉到的元件。在此情況下，行動服務是確保您的工作負載如您所預期受到保護的關鍵。

我們如何才能以最佳化的方式，在某些 OMS 元件的協助之下，確保我們有可靠的受保護安裝程式？

在本文中，我們會提供如何一起運用 OMS Automation DSC 與 OMS Site Recovery 的範例，以確保行動服務和 Azure VM 代理程式部署至您想要保護的 Windows 電腦，而且在 Azure 為複寫目標時一直持續執行。

## 必要條件

- 用來儲存必要安裝程式的儲存機制
- 用來儲存必要複雜密碼以向管理伺服器註冊的儲存機制
- 在您想要啟用保護的電腦上安裝 Windows Management Framework 5.0 (OMS Automation DSC 的需求)

如果您想要對採用 WMF 4.0 的 Windows 電腦使用 DSC，請參閱「在中斷連線的環境中使用 DSC」一節

(每部管理伺服器都會產生唯一的複雜密碼。如果您即將部署多部管理伺服器，您必須確保在 passphrase.txt 檔案中儲存正確的複雜密碼)

行動服務可以透過命令列安裝並接受數個引數。

這就是我們為何需要有二進位檔案 (在從我們的安裝擷取它們之後)，並將它們儲存在能夠使用 DSC 組態進行擷取的地方。

## 步驟 1：擷取二進位檔

1. 若要擷取此安裝所需的檔案，請瀏覽至管理伺服器上的下列目錄︰**\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**

    在此資料夾中，您應會看到一個名為的 MSI 檔案︰

    **Microsoft-ASR\_UA\_<version>_Windows\_GA_<date>\_Release.exe**

    使用下列 Cmdlet 來擷取安裝程式：

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. 選取所有檔案並傳送至已壓縮的資料夾。

這樣就大功告成了！ 您現在有二進位檔，您必須使用 OMS Automation DSC 自動安裝行動服務。

### 複雜密碼
接著，您必須決定要放置此壓縮資料夾的位置。在我的案例中，我稍後將示範我在 Azure 中使用儲存體帳戶，我也在其中放置我需要用於安裝的複雜密碼，所以代理程式會在過程中向管理伺服器註冊程序。

您在部署管理伺服器時取得的複雜密碼可以儲存為 passphrase.txt 檔案。

我已在我的 Azure 儲存體帳戶中的專用容器中放置壓縮的資料夾和複雜密碼。

![資料夾位置](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

如果您想要將這些檔案保留在您網路中的共用上，您絕對可以這樣做。您只需要確保我們稍後實際使用的 DSC 資源具有存取權，而且可以取得安裝和複雜密碼。

## 步驟 2 – 建立 DSC 組態
我的設定取決於 WMF 5.0，這表示，為了讓電腦能夠順利透過 OMS Automation DSC 套用組態，必須存在 WMF 5.0。

我的環境中使用下列 DSC 組態︰

```
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
```
此組態將會進行下列各項：

- 變數會告訴組態哪裡可取得行動服務和 Azure VM 代理程式的二進位檔和複雜密碼，以及可儲存輸出的位置。
- 匯入 xPSDesiredStateConfiguration DscResource，以便我們使用 'xRemoteFile' 從儲存機制下載檔案。
- 建立我們想要用來儲存二進位檔的目錄。
- 封存資源將從壓縮的資料夾解壓縮檔案。
- 封裝 ‘Install’ 資源將使用特定引數從 UNIFIEDAGENT.EXE 安裝程式安裝行動服務 (建構引數的變數必須變更，以反映您的環境)。
- 封裝 'AzureAgent' 將在 Azure 中執行的每部 VM 上安裝建議的 Azure VM 代理程式，也可以在容錯移轉後將擴充功能新增至 VM。
- 服務資源將可確保相關的行動服務與 Azure 服務一直執行中。

我已在我的電腦上的資料夾中將組態儲存為 **ASRMobilityService**。

(請記得取代組態中的 CSIP，以反映實際的管理伺服器，以便代理程式會正確連接，而且使用正確的複雜密碼)。

## 步驟 3 – 上傳至 OMS Automation DSC

由於我們所產生的 DSC 組態會匯入必要的 DSC 資源模組 (xPSDesiredStateConfiguration)，所以您必須在上傳 DSC 組態之前，在 OMS Automation 中匯入該模組。

登入您的自動化帳戶並巡覽至 AssetsàModules，然後按一下 [瀏覽資源庫]。

您可以在這裡搜尋模組並將它匯入您的帳戶中。

![匯入模組](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

完成之後，請前往您已安裝 Azure RM 模組的電腦，並繼續匯入新建立的 DSC 組態。

### 匯入 Cmdlet

在 PowerShell 中，登入您的 Azure 訂用帳戶並修改 Cmdlet，以反映您的環境。

首先，我想要使用下列 Cmdlet 將組態上傳至 OMS Automation DSC︰

```
Import-AzureRmAutomationDscConfiguration `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -SourcePath C:\ASR\ASRMobilityService.ps1 `
                                        -Published `
                                        -Description "DSC Config for Mobility Service"
```

接下來，我們需要在 OMS Automation DSC 中編譯組態，以便開始註冊節點。

### 在 OMS Automation DSC 中編譯組態

我們會藉由執行下列 Cmdlet 來達成目的：

```
Start-AzureRmAutomationDscCompilationJob `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -ConfigurationName ASRMobilityService
```

這可能需要幾分鐘的時間，因為我們基本上會將組態部署至裝載的 DSC 提取服務。

完成後，您可以使用 PowerShell (Get-AzureRmAutomationDscCompilationJob) 或使用 portal.azure.com 來擷取作業資訊。

![擷取作業](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

我們現在已成功發佈 DSC 組態並將其上傳至 OMS Automation DSC。

## 步驟 4 – 將電腦上架至 OMS Automation DSC
*完成此案例的其中一個必要條件是使用最新版的 WMF 更新您的 Windows 電腦。您可以遵循此 URL，下載並安裝正確版本的平台︰https://www.microsoft.com/download/details.aspx?id=50395*

現在，我們將為 DSC 建立將套用至我們的節點的 metaconfig。若要順利完成此作業，您必須針對在 Azure 中選取的自動化帳戶擷取端點 URL 和主要金鑰。

這些值可以位於自動化帳戶 [所有設定] 刀鋒視窗上的 [金鑰] 之下。

![金鑰值](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

我的環境中有想要使用 OMS Site Recovery 保護的 Windows Server 2012 R2 實體伺服器。

在我們開始建立伺服器與自動化 DSC 端點的關聯之前，建議檢查登錄中是否有任何擱置的檔案重新命名作業，因為這可能會由於擱置的重新開機而阻止安裝程式完成。

### 檢查登錄中是否有任何擱置的檔案重新命名作業

執行下列 Cmdlet 以確認伺服器上沒有擱置的重新開機︰

```
Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager" | select PendingFileRenameOperations
```
如果呈現空白，您就可以繼續作業。若非如此，您必須在維護期間重新啟動伺服器以解決此問題。

為了在伺服器上套用組態，我啟動 PowerShell ISE 並執行下列指令碼。這基本上是 DSC 本機組態，該組態會指示本機組態管理員引擎向 OMS Automation DSC 服務註冊並擷取特定的組態 (ASRMobilityService.localhost)

```
[DSCLocalConfigurationManager()]

Configuration metaconfig
{
    node localhost
    {

        Settings
        {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }
                ResourceRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }
                ConfigurationRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
                ConfigurationNames = 'ASRMobilityService.localhost'
            }
                ReportServerWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }

    }
}
metaconfig

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

此組態會設定本機組態管理員向 OMS Automation DSC 註冊本身，且基本上會指示引擎如何運作、在組態漂移 (ApplyAndAutoCorrect) 以及需要重新開機的情況下該怎麼做，之後繼續進行設定。

一旦您執行此動作，節點應該開始向 Automation DSC 註冊

![註冊節點](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

如果我們回到 portal.azure.com，我們可以看到新註冊的節點現已出現在入口網站中。

![註冊節點](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

在伺服器上，我們可以執行下列 PowerShell Cmdlet 來確認它已正確註冊︰

```
Get-DscLocalConfigurationManager
```

提取此組態並套用至伺服器後，您可以執行下列程式碼來確認這點︰

```
Get-DscConfigurationStatus
```

輸出會顯示伺服器已成功提取其組態︰

![註冊節點](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

此外，行動服務安裝程式有自己的記錄檔，您可在 ‘<SystemDrive>\\ProgramData\\ASRSetupLogs’ 找到。

這樣就大功告成了 – 我們現在已在想要使用 Site Recovery 保護的電腦上成功部署和註冊行動服務，而且我們可以依賴一直持續執行必要服務的 DSC。

![註冊節點](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

在管理伺服器偵測到行動服務後，您可以繼續使用 Site Recovery 在電腦上設定保護及啟用複寫。

## 在中斷連線的環境中使用 DSC

如果您的電腦未連線到網際網路，您仍可依賴 DSC，以在您想要保護的工作負載上部署和設定行動服務。

您可以在您的環境中具現化自己的 DSC 提取伺服器，其基本上提供與您從 OMS Automation DSC 取得的相同功能 – 向 DSC 端點註冊後，用戶端將會提取組態。不過，另一個選項是使用推送，您可以將 DSC 組態手動推送至您的電腦 (在本機或遠端)。

請注意，在此範例中，我們新增了 computername 的參數，而遠端檔案現在位於您要保護的電腦應可存取的遠端共用上，而我們會在指令碼的結尾制定組態，然後開始將 DSC 組態套用到目標電腦。

### 必要條件

· 安裝 xPSDesiredStateConfiguration PowerShell 模組

對於 WMF 5.0 安裝所在的 Windows 電腦，您可以在目標電腦上執行下列 Cmdlet，僅只安裝 xPSDesiredStateConfiguration 模組：

```
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

如果您需要將此模組發佈至採用 WMF 4.0 的 Windows 電腦，您也可以在 PowerShellGet (WMF 5.0) 所在的電腦上執行下列 Cmdlet 來下載和儲存模組︰

```
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

對於 WMF 4.0，也要確保電腦上已安裝下列更新︰

https://www.microsoft.com/download/details.aspx?id=40749

下列組態可以推送至採用 WMF 5.0 和 4.0 的 Windows 電腦

```
configuration ASRMobilityService {

    param (
    [Parameter(Mandatory=$true)]
    $computername
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = "C:\Temp\Mobility_service\passphrase.txt"
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $computername {      

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
ASRMobilityService

Start-DscConfiguration -ComputerName $computername .\ASRMobilityService -wait -force -Verbose
```

如果您想要在您的公司網路內具現化自己的 DSC 提取伺服器，以模擬您可以從 OMS Automation DSC 取得的相同功能，請參閱下列指南︰https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396

## 選擇性：使用 Azure Resource Manager 範本部署 DSC 組態

在本文中，我們到目前為止將重點放在如何建立自己的 DSC 組態來自動部署行動服務和 Azure VM 代理程式，並確保它們在您想要保護的電腦上執行。此外，我們也有 Azure Resource Manager 範本可將此 DSC 組態部署到新的或現有的 Azure 自動化帳戶，並透過範本中的輸入參數建立將會包含環境變數的自動化資產。

部署後，您可以只參考本指南中的步驟 4 將您的電腦上架。

此範本會執行下列作業︰

· 使用現有或建立新的 OMS 自動化帳戶

· 採用您的輸入參數︰

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


所有上述步驟都會依正確順序進行，以便您輕鬆地開始將電腦上架以便進行保護。

包含部署指示的範本位於︰

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC

使用 PowerShell 進行部署︰

```
$OMSAutomationRGname = 'KNOMS'

$DSCJobGuid = (New-Guid).Guid

New-AzureRmResourceGroupDeployment `
                                  -Name "DSC3" `
                                  -ResourceGroupName $OMSAutomationRGname `
                                  -TemplateFile https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json `
                                  -OMSAutomationAccountName KNOMSAA `
                                  -ASRRemoteFile "https://knrecstor01.blob.core.windows.net/asr/ASR.zip" `
                                  -ASRRemotePassphrase "https://knrecstor01.blob.core.windows.net/asr/passphrase.txt" `
                                  -ASRCSEndpoint '10.0.0.115' `
                                  -DSCJobGuid $DSCJobGuid `
                                  -Verbose
```

## 後續步驟：

部署行動服務代理程式之後，您可以繼續對虛擬機器[啟用複寫](site-recovery-vmware-to-azure.md#step-6-replicate-applications)。

<!---HONumber=AcomDC_0810_2016---->