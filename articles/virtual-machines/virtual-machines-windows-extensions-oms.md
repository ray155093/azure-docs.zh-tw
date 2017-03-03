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
ms.date: 01/08/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 251d7b973426afb50206c428873021144b8bffdf
ms.openlocfilehash: 63e2509b92b4d97bfdc98629cc356816839b03b5
ms.lasthandoff: 01/11/2017


---
# <a name="oms-virtual-machine-extension-for-windows"></a>適用於 Windows 的 OMS 虛擬機器擴充功能

Operations Management Suite (OMS) 可提供雲端和內部部署資產的監視、警示和警示補救功能。 Microsoft 已發佈和支援適用於 Windows 的 OMS 代理程式虛擬機器擴充功能。 擴充功能會在 Azure 虛擬機器上安裝 OMS 代理程式，並且在現有的 OMS 工作區中註冊虛擬機器。 本文件詳述適用於 Windows 的 OMS 虛擬機器擴充功能所支援的平台、組態和部署選項。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統
適用於 Windows 的 OMS 代理程式擴充功能可以在 Windows Server 2008 R2、2012、2012 R2 及 2016 版本上執行。

### <a name="internet-connectivity"></a>網際網路連線
適用於 Windows 的 OMS 代理程式擴充功能會要求目標虛擬機器連接到網際網路。 

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 OMS 代理程式擴充功能的結構描述。 此擴充功能需要來自目標 OMS 工作區的工作區識別碼和工作區金鑰，這些在 OMS 入口網站上皆有提供。 由於工作區金鑰應視為敏感性資料，因此應儲存在受保護的設定組態中。 Azure VM 擴充功能的受保護設定資料會經過加密，而只有在目標虛擬機器上才會解密。

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
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| 類型 | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (例如) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (例如) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節中詳述的 JSON 結構描述可在部署 Azure Resource Manager 範本時，在 Azure Resource Manager 範本中用來執行 OMS 代理程式擴充功能。 在 [Azure 快速入門資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)上可以找到包含 OMS 代理程式 VM 擴充功能的範例範本。 

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

從 Azure 入口網站及透過使用 Azure PowerShell 模組，都可擷取有關擴充功能部署狀態的資料。 若要查看所指定 VM 的擴充功能部署狀態，請使用 Azure PowerShell 模組來執行下列命令。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

擴充功能執行輸出會記錄至下列目錄中的檔案︰

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/en-us/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/en-us/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/en-us/support/faq/)。

