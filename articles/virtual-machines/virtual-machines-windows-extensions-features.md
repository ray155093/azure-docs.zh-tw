---
title: "虛擬機器擴充功能和功能 | Microsoft Docs"
description: "了解哪些擴充功能適用於 Azure 虛擬機器，並依它們提供或改善的內容來分組。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: b8add02b0180f46eb59843c31dbcfe8a7ff950c4
ms.openlocfilehash: 62cce8089982722b92b3992f4c6a7b68f6f3794d


---
# <a name="about-virtual-machine-extensions-and-features"></a>有關虛擬機器擴充功能和功能

「Azure 虛擬機器」擴充功能是小型的應用程式，可在「Azure 虛擬機器」上提供部署後設定及自動化工作。 例如，如果「虛擬機器」要求安裝軟體、防毒保護或 Docker 組態，便可使用 VM 擴充功能來完成這些工作。 您可以使用 Azure CLI、PowerShell、Resource Manager 範本及 Azure 入口網站來執行 Azure VM 擴充功能。 擴充功能可以與新的虛擬機器部署搭配，或是在任何現有的系統上執行。

本文件提供虛擬機器擴充功能、使用 Azure 虛擬機器擴充功能之必要條件，以及如何偵測、管理和移除虛擬機器擴充功能之指引的概觀。 因為有許多可用的 VM 擴充功能，每個都有可能唯一的組態，因此本文件提供通用的資訊。 可以在每份個別擴充功能專用的文件中找到擴充功能特定的詳細資料。 

## <a name="use-cases-and-samples"></a>使用案例和範例

有許多不同的可用 Azure VM 擴充功能，各有特定使用案例。 部分範例使用案例包括︰

- 使用適用於 Windows 的 DSC 擴充功能將 PowerShell 預期狀態設定套用至虛擬機器。 如需詳細資訊，請參閱 [Azure 期望狀態組態擴充功能簡介](virtual-machines-windows-extensions-dsc-overview.md)。
- 使用 Microsoft 監視代理程式 VM 擴充功能設定監視虛擬機器。 如需詳細資訊，請參閱[將 Azure 虛擬機器連接到 Log Analytics](../log-analytics/log-analytics-azure-vm-extension.md)。 
- 使用 Datadog 擴充功能設定監視您的 Azure 基礎結構。 如需詳細資訊，請參閱 [Datadog 部落格](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)。
- 使用 Chef 設定 Azure 虛擬機器。 如需詳細資訊，請參閱[使用 Chef 自動化 Azure 虛擬機器部署](virtual-machines-windows-chef-automation.md)。

除了處理序特定擴充功能以外，自訂指令碼擴充功能同時適用於 Windows 和 Linux 虛擬機器。 適用於 Windows 的自訂指令碼擴充功能允許在虛擬機器上執行任何 PowerShell 指令碼。 這對於設計需要超過原生 Azure 工具可提供之組態的 Azure 部署很有用。 如需詳細資訊，請參閱 [Windows VM 自訂指令碼擴充功能](virtual-machines-windows-extensions-customscript.md)。

若要逐步解說在端對端應用程式部署中使用 VM 擴充功能的範例，請參閱[將應用程式自動部署到 Azure 虛擬機器](virtual-machines-windows-dotnet-core-1-landing.md)。

## <a name="prerequisites"></a>必要條件

每個虛擬機器擴充功能可能有它自己的必要條件組。 例如，Docker VM 擴充功能有受支援 Linux 散發套件的必要條件。 個別擴充功能的需求會在擴充功能特定的文件中詳細說明。 

### <a name="azure-vm-agent"></a>Azure VM 代理程式
「Azure VM 代理程式」可管理「Azure 虛擬機器」與「Azure 網狀架構控制器」之間的互動。 VM 代理程式負責部署和管理「Azure 虛擬機器」的許多功能層面，包括執行「VM 擴充功能」。 「Azure VM 代理程式」會預先安裝在「Azure 資源庫映像」上，並可安裝在支援的作業系統上。 

如需有關支援的作業系統和安裝指示，請參閱 [Azure 虛擬機器代理程式](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="discover-vm-extensions"></a>探索 VM 擴充功能
有許多不同的 VM 擴充功能可供與「Azure 虛擬機器」搭配使用。 若要查看完整清單，請使用 Azure PowerShell 模組執行下列命令。

```powershell
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

## <a name="running-vm-extensions"></a>執行 VM 擴充功能

Azure 虛擬機器擴充功能可以在現有的虛擬機器上執行，這在需要進行組態變更或復原已部署之 VM 上的連線時很有用。 VM 擴充功能也可以搭配 Azure Resource Manager 範本部署。 使用擴充功能搭配 Resource Manager 範本，可在不需要後置部署介入的情況下部署與設定 Azure 虛擬機器。

下列方法可用來針對現有的虛擬機器執行擴充功能。 

### <a name="powershell"></a>PowerShell

有數個 PowerShell 命令可用來執行個別的擴充功能。 若要查看清單，請執行下列 PowerShell 命令：

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

這會提供類似下列的輸出︰

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

下列範例會使用自訂指令碼擴充功能，從 GitHub 存放庫下載指令碼到目標虛擬機器上，然後執行該指令碼。 如需有關 VM 存取擴充功能的詳細資訊，請參閱[自訂指令碼擴充功能概觀](virtual-machines-windows-extensions-customscript.md)。

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

在此範例中，VM 存取擴充功能會用來重設 Windows 虛擬機器的系統管理密碼。 如需有關 VM 存取擴充功能的詳細資訊，請參閱[重設 Windows VM 中的遠端桌面服務](virtual-machines-windows-reset-rdp.md)。

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzureRmVMExtension` 命令可用來作為啟動任何 VM 擴充功能的全部攔截或一般命令。 如需詳細資訊，請參閱 [Set-AzureRmVMExtension 參考](https://msdn.microsoft.com/en-us/library/mt603745.aspx)。


### <a name="azure-portal"></a>Azure 入口網站

VM 擴充功能可以透過 Azure 入口網站套用至現有的虛擬機器。 若要這樣做，請選取虛擬機器，選擇 [擴充功能]，然後按一下 [新增]。 這樣會提供可用的擴充功能清單。 選取其中一個想要的擴充功能，系統將會提供組態精靈。 

下圖將顯示從 Azure 入口網站安裝 Microsoft 反惡意程式碼擴充功能。

![反惡意程式碼擴充功能](./media/virtual-machines-windows-extensions-features/anti-virus-extension.png)

### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

VM 擴充功能可以新增至 Azure Resource Manager 範本，並使用範本的部署執行。 使用範本部署擴充功能，針對建立完整設定的 Azure 部署很有用。 例如，下列 JSON 是取自部署一組負載平衡虛擬機器及 Azure SQL Database，並在每個 VM 上安裝 .Net Core 應用程式的 Resource Manager 範本。 VM 擴充功能會處理軟體安裝。 

您可以在[這裡](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)找到完整的 Resource Manager 範本。 

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

如需詳細資訊，請參閱[使用 Windows VM 擴充功能編寫 Azure Resource Manager 範本](virtual-machines-windows-extensions-authoring-templates.md)。

## <a name="securing-vm-extension-data"></a>保護 VM 擴充功能資料

當執行 VM 擴充功能時，可能需要包含機密資訊，例如認證、儲存體帳戶名稱和儲存體帳戶存取金鑰。 許多 VM 擴充功能包含受保護的組態，該組態會加密資料並只在目標虛擬機器內加以解密此資料。 每個個別的擴充功能具有特定受保護的組態結構描述，每一個都會在擴充功能的特定文件中詳細說明。

下列範例會說明適用於 Windows 的自訂指令碼擴充功能執行個體。 請注意，要執行的命令包含一組認證。 在此範例中，要執行的命令不會加密。


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

將命令移至受保護組態的執行屬性，可保護執行字串。

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>針對 VM 擴充功能進行疑難排解

每個 VM 擴充功能可能會有擴充功能特定的疑難排解步驟。 例如，當使用自訂指令碼延伸模組時，指令碼執行詳細資料可以在擴充功能執行所在的虛擬機器上本機找到。 所有擴充功能特定的疑難排解步驟都會在擴充功能特定文件中詳述。 

下列疑難排解步驟適用於所有虛擬機器擴充功能。

### <a name="viewing-extension-status"></a>檢視擴充功能狀態

一旦虛擬機器擴充功能都已針對虛擬機器執行，請使用下列 PowerShell 命令傳回擴充功能的狀態。 請以您自己的值取代範例參數名稱。 `Name` 參數會使用在執行階段指定給擴充功能的名稱。

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

輸出大致如下：

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          : 
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

也可以在 Azure 入口網站中找到擴充功能的執行狀態。 若要檢視擴充功能的狀態，請選取虛擬機器 > [擴充功能] > 選取所需的擴充功能。

### <a name="rerunning-vm-extensions"></a>重新執行 VM 擴充功能 

可能會發生必須重新執行虛擬機器擴充功能的情況。 這可以藉由移除擴充功能，然後使用您選擇的執行方法重新執行擴充功能來完成。 若要移除擴充功能，請使用 Azure PowerShell 模組執行下列命令。 請以您自己的值取代範例參數名稱。

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

擴充功能也可以使用 Azure 入口網站來移除。 若要這樣做，請選取虛擬機器 > [擴充功能] > 想要的擴充功能 > [解除安裝]。

## <a name="common-vm-extensions-reference"></a>常見的 VM 擴充功能參考
| 擴充功能名稱 | 說明 | 相關資訊 |
| --- | --- | --- |
| Windows 的自訂指令碼延伸模組 |對「Azure 虛擬機器」執行指令碼 |[Windows 的自訂指令碼延伸模組](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Windows 的 DSC 延伸模組 |PowerShell DSC (所需的狀態組態) 延伸模組。 |[適用於 Windows 的 DSC 擴充功能](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure 診斷擴充功能 |管理「Azure 診斷」 |[Azure 診斷擴充功能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM 存取擴充功能 |管理使用者和認證 |[適用於 Windows 的 VM 存取擴充功能](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |



<!--HONumber=Nov16_HO5-->


