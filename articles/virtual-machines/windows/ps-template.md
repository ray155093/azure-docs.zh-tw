---
title: "從 Azure 中的範本建立 Windows VM | Microsoft Docs"
description: "使用 Resource Manager 範本和 PowerShell 輕鬆地建立新的 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: davidmu
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 67b023ccd761bc08b96c9ebda907c6451bfbe52f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/17/2017


---

# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>利用 Resource Manager 範本建立 Windows 虛擬機器

本文說明如何使用 PowerShell 來部署 Azure Resource Manager 範本。 此[範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)會在具有單一子網路的新虛擬網路中，部署執行 Windows Server 的單一虛擬機器。

如需虛擬機器資源的詳細說明，請參閱 [Azure Resource Manager 範本中的虛擬機器 (英文)](template-description.md)。 如需有關範本中所有資源的詳細資訊，請參閱 [Azure Resource Manager 範本逐步解說](../../azure-resource-manager/resource-manager-template-walkthrough.md)。

執行本文中的步驟約需 5 分鐘的時間。

## <a name="step-1-install-azure-powershell"></a>步驟 1：安裝 Azure PowerShell

如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](../../powershell-install-configure.md)。

## <a name="step-2-create-a-resource-group"></a>步驟 2：建立資源群組

所有資源都必須部署在[資源群組](../../azure-resource-manager/resource-group-overview.md)中。

1. 取得可以建立資源的可用位置清單。
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. 在您選取的位置中建立資源群組。 此範例示範在 [美國中部] 位置中建立名為 **myResourceGroup** 的資源群組：

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
    ```
   
  您應該會看到如下列範例的結果：

    ```powershell 
    ResourceGroupName : myResourceGroup
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
    ```

## <a name="step-3-create-the-resources"></a>步驟 3：建立資源
部署範本並在出現提示時提供參數值。 此範例會在您建立的資源群組中部署 101-vm-simple-windows 範本：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
系統會要求您提供 VM 上系統管理員帳戶的名稱、該帳戶的密碼，以及 DNS 首碼。

您應該會看到如下列範例的結果：

    DeploymentName    : azuredeploy
    ResourceGroupName : myResourceGroup
    ProvisioningState : Succeeded
    Timestamp         : 12/29/2016 8:11:37 PM
    Mode              : Incremental
    TemplateLink      :
       Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                        101-vm-simple-windows/azuredeploy.json
       ContentVersion : 1.0.0.0
    Parameters        :
      Name             Type                       Value
      ===============  =========================  ==========
      adminUsername    String                     myAdminUser
      adminPassword    SecureString
      dnsLabelPrefix   String                     myDomain
      windowsOSVersion String                     2016-Datacenter
    Outputs           :
      Name             Type                       Value
      ===============  =========================  ===========
      hostname         String                     myDomain.centralus.cloudapp.azure.com
    DeploymentDebugLogLevel :

> [!NOTE]
> 您也可以從本機檔案部署範本和參數。 若要深入了解，請參閱[搭配使用 Azure PowerShell 與 Azure 儲存體](../../storage/storage-powershell-guide-full.md)。

## <a name="next-steps"></a>後續步驟

- 如果部署有問題，下一個步驟就是查看[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解 (英文)](../../resource-manager-common-deployment-errors.md)。
- 透過[使用 Resource Manager 和 PowerShell 建立 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 了解如何使用 Azure PowerShell 來建立虛擬機器。
- 檢閱[使用 Azure PowerShell 模組來建立和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，以了解如何管理您建立的虛擬機器。


