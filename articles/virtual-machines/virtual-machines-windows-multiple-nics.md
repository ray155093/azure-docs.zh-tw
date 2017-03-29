---
title: "連接多個 NIC 的 Windows VM | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 或 Resource Manager 範本，來建立連接多個 NIC 的 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 95b2820d2f68be34cca7b8d414c581ba44a29804
ms.lasthandoff: 03/17/2017


---
# <a name="create-a-windows-vm-with-multiple-nics"></a>建立具有多個 NIC 的 Windows VM
您可以在 Azure 中，建立連接多個虛擬網路介面 (NIC) 的虛擬機器 (VM)。 常見案例是有不同的子網路可用於前端和後端連線，或者專門用來監視或備份解決方案的網路。 本文提供快速命令來建立連接多個 NIC 的 VM。 如需詳細資訊，包括如何在自己的 PowerShell 指令碼內建立多個 NIC，請深入了解 [部署多個 NIC 的 VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)。 不同的 [VM 大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 支援不同數量的 NIC，因此可據以調整您的 VM。

## <a name="create-core-resources"></a>建立核心資源
確定您已 [安裝並執行最新的 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 登入您的 Azure 帳戶：

```powershell
Login-AzureRmAccount
```

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`mystorageaccount` 和 `myVM`。

首先，建立資源群組。 下列範例會在 `WestUs` 位置建立名為 `myResourceGroup` 的資源群組：

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

建立儲存體帳戶以放置您的 VM。 下列範例會建立名為 `mystorageaccount` 的儲存體帳戶：

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>建立虛擬網路和子網路
定義兩個虛擬網路子網路 - 一個用於前端流量，另一個用於後端流量。 以下範例將定義兩個子網路，名為 `mySubnetFrontEnd`和 `mySubnetBackEnd`：

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

建立虛擬網路和子網路。 下列範例會建立名為 `myVnet`的虛擬網路：

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>建立多個 NIC
建立兩個 NIC，將一個 NIC 連接到前端子網路，並將另一個 NIC 連接到後端子網路。 下列範例會建立兩個 NIC，名為 `myNic1`和 `myNic2`：

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

通常您也可以建立[網路安全性群組](../virtual-network/virtual-networks-nsg.md)或[負載平衡器](../load-balancer/load-balancer-overview.md)來協助管理，以及將流量分散到您的 VM。 這篇有關 [多個 NIC 的 VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) 的更詳盡文章可逐步引導您建立網路安全性群組並指派 NIC。

## <a name="create-the-virtual-machine"></a>建立虛擬機器
現在開始建置您的 VM 組態。 在每個 VM 大小中，您可以新增至 VM 的 NIC 總數是有限制的。 深入了解 [Windows VM 大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

首先，將您的 VM 認證設定至 `$cred` 變數，如下所示︰

```powershell
$cred = Get-Credential
```

下列範例會定義名為 `myVM`的 VM，並且使用最多支援兩個 NIC 的 VM 大小 (`Standard_DS2_v2`)：

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

建立 VM 組態的其餘部分。 下列範例會建立 Windows Server 2012 R2 VM：

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

連接您先前建立的兩個 NIC：

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

針對新的 VM 設定儲存體和虛擬磁碟：

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

最後，建立 VM：

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="add-a-nic-to-an-existing-vm"></a>將 NIC 新增至現有的 VM

您現在可以將 NIC 新增至現有的 VM。 若要使用此功能，您必須先使用下列 Stop-AzureRmVM Cmdlet 來解除配置 VM。

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

接下來，使用 Get-AzureRmVM Cmdlet 取得 VM 現有的組態

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

您可以在**與 VM 相同的 VNET** 中建立新的 NIC (如本文開頭所示)，或連接現有的 NIC。 我們假設您要在 VNET 中連接現有的 NIC `MyNic3`。 

```powershell
$nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId -Primary | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
```

> [!NOTE]
> 您必須在具有多個 NIC 的 VM 上將其中一個 NIC 設為主要，如此才能讓我們將新的 NIC 設為主要。 如果您在 VM 上的前一個 NIC 是主要的，則不需要指定 -Primary 參數。 如果您想要在 VM 上切換主要 NIC，請依照下列步驟執行

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"

# Find out all the NICs on the VM and find which one is Primary
$vm.NetworkProfile.NetworkInterfaces

# Set the NIC 0 to be primary
$vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
$vm.NetworkProfile.NetworkInterfaces[1].Primary = $false

# Update the VM state in Azure
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
```

## <a name="remove-a-nic-from-an-existing-vm"></a>從現有的 VM 移除 NIC

您也可以從 VM 移除 NIC。 若要使用此功能，您必須先使用下列 Stop-AzureRmVM Cmdlet 來解除配置 VM。

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

接下來，使用 Get-AzureRmVM Cmdlet 取得 VM 現有的組態

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

現在檢視 VM 上的所有 NIC，並複製您要移除之 NIC 的名稱

```powershell
$vm.NetworkProfile.NetworkInterfaces

Remove-AzureRmNetworkInterface -Name "myNic3" -ResourceGroupName "myResourceGroup"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>使用 Resource Manager 範本建立多個 NIC
Azure Resource Manager 範本會使用宣告式 JSON 檔案來定義您的環境。 您可以閱讀 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。 Resource Manager 範本提供一種方式，可在部署期間建立資源的多個執行個體，例如建立多個 NIC。 您使用 *copy* 來指定要建立的執行個體數目：

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

深入了解[使用 *copy* 建立多個執行個體](../azure-resource-manager/resource-group-create-multiple.md)。 

您也可以使用 `copyIndex()`，然後在資源名稱後面附加一個數字，讓您能夠建立 `myNic1`、`MyNic2`，依此類推。以下顯示附加索引值的範例：

```json
"name": "[concat('myNic', copyIndex())]", 
```

您可以閱讀 [使用 Resource Manager 範本建立多個 NIC](../virtual-network/virtual-network-deploy-multinic-arm-template.md)的完整範例。

## <a name="next-steps"></a>後續步驟
嘗試建立具有多個 NIC 的 VM 時，請務必檢閱 [Windows VM 大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 。 注意每個 VM 大小所支援的 NIC 數目上限。 



