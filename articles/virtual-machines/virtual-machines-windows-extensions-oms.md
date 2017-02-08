---
title: "適用於 Windows 的 OMS Azure 虛擬機器擴充功能 | Microsoft Docs"
description: "使用虛擬機器擴充功能在 Windows 虛擬機器上部署 OMS 代理程式。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: d65505182267bec053fca0ab5045cbcae9381ac7
ms.openlocfilehash: 245e49d36142ef8e927a5e494113e4dd7809fc16


---
# <a name="oms-virtual-machine-extension-for-windows"></a>適用於 Windows 的 OMS 虛擬機器擴充功能

## <a name="overview"></a>概觀

Operations Management Suite (OMS) 可提供雲端和內部部署資產的監視、警示和警示補救功能。 Microsoft 已發佈和支援適用於 Windows 的 OMS 代理程式虛擬機器擴充功能。 擴充功能會在 Azure 虛擬機器上安裝 OMS 代理程式，並且在現有的 OMS 工作區中註冊虛擬機器。 本文件詳述適用於 Windows 的 OMS 虛擬機器擴充功能所支援的平台、組態和部署選項。

如需 Azure 虛擬機器擴充功能的一般資訊，請參閱[虛擬機器擴充功能概觀](./virtual-machines-windows-extensions-features.md)。

如需 Operations Management Suite 的詳細資訊，請參閱 [Operations Management Suite 概觀](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite)。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統

可以對 Windows Server 2012、2012 R2 和 2016版執行適用於 Windows 的 OMS 代理程式擴充功能。

### <a name="connectivity"></a>連線能力

適用於 Windows 的 OMS 代理程式擴充功能會要求目標虛擬機器連接到網際網路。 

## <a name="extension-configuration"></a>擴充功能組態

適用於 Windows 的 OMS 代理程式虛擬機器擴充功能需要來自目標 OMS 工作區的工作區識別碼和工作區金鑰。 工作區金鑰應被視為敏感性資料，所以會儲存在受保護的組態中。 Azure VM 擴充功能保護的組態資料會經過加密，只會在目標虛擬機器上解密。 公用和私人組態會在部署階段指定，本文件的後續章節有詳細說明。

### <a name="public-configuration"></a>公用組態

公用組態的結構描述︰

- workspaceId：(必要字串) 虛擬機器要上架的 OMS 工作區識別碼。

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>私人組態

公用組態的結構描述︰

- workspaceKey：(必要字串) 工作區的主要/次要共用金鑰。

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後設定的虛擬機器時，很適合使用範本。 在 [Azure 快速啟動資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)上可找到包含 OMS 代理程式 VM 擴充功能的範例 Resource Manager 範本。 

使用此按鈕，就可以從這份文件部署這個範例︰

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-windows-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

用來部署 OMS 代理程式 VM 擴充功能的 JSON 外觀類似以下的 JSON 範例︰

```json
{
    "type": "extensions",
    "name": "Microsoft.EnterpriseCloud.Monitoring",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceKey"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceId"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 部署

`Set-AzureRmVMExtension` 命令可以用來將 OMS 代理程式虛擬機器擴充功能部署到現有的虛擬機器。 執行命令之前，必須將公用和私人組態儲存在 PowerShell 雜湊表中。 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS ` 
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

擴充功能執行輸出會記錄至下列目錄中的檔案︰

`
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
`

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/en-us/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/en-us/support/faq/)。



<!--HONumber=Dec16_HO1-->


