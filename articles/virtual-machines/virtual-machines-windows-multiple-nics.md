---
title: 在 Windows VM 上設定多個 NIC | Microsoft Docs
description: 了解如何使用 Azure PowerShell 或 Resource Manager 範本，來建立連接多個 NIC 的 VM。
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2016
ms.author: iainfou

---
# 建立具有多個 NIC 的 VM
您可以在 Azure 中，建立連接多個虛擬網路介面 (NIC) 的虛擬機器 (VM)。常見案例是有不同的子網路可用於前端和後端連線，或者專門用來監視或備份解決方案的網路。本文提供快速命令來建立連接多個 NIC 的 VM。如需詳細資訊，包括如何在自己的 PowerShell 指令碼內建立多個 NIC，請深入了解[部署多個 NIC 的 VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)。不同的 [VM 大小](virtual-machines-windows-sizes.md)支援不同數量的 NIC，因此可據以調整您的 VM。

> [!WARNING]
> 當您建立 VM 時，必須連接多個 NIC - 您無法在現有的 VM 中新增 NIC。您可以[根據原始虛擬磁碟建立新的 VM](virtual-machines-windows-specialized-image.md)，並且在部署 VM 時建立多個 NIC。
> 
> 

## 建立核心資源
確定您已[安裝並執行最新的 Azure PowerShell](../powershell-install-configure.md)。

首先，建立資源群組：

```powershell
New-AzureRmResourceGroup -Name TestRG -Location WestUS
```

建立儲存體帳戶以放置您的 VM：

```powershell
$storageAcc = New-AzureRmStorageAccount -Name teststorage `
    -ResourceGroupName TestRG -Kind Storage -SkuName Premium_LRS -Location WestUS
```

## 建立虛擬網路和子網路
定義兩個虛擬網路子網路 - 一個用於前端流量，另一個用於後端流量。使用這些子網路建立虛擬網路：

```powershell
$frontEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" `
    -AddressPrefix 192.168.1.0/24
$backEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "BackEnd" `
    -AddressPrefix 192.168.2.0/24


$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location WestUS `
    -Subnet $frontEndSubnet,$backEndSubnet
```


## 建立多個 NIC
建立兩個 NIC，將一個 NIC 連接到前端子網路，並將另一個 NIC 連接到後端子網路：

```powershell
$frontEnd = $vnet.Subnets|?{$_.Name -eq 'FrontEnd'}
$NIC1 = New-AzureRmNetworkInterface -Name NIC1 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $FrontEnd.Id

$backEnd = $vnet.Subnets|?{$_.Name -eq 'BackEnd'}
$NIC2 = New-AzureRmNetworkInterface -Name NIC2 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $BackEnd.Id
```

通常您也可以建立[網路安全性群組](../virtual-network/virtual-networks-nsg.md)或[負載平衡器](../load-balancer/load-balancer-overview.md)來協助管理，以及將流量分散到您的 VM。這篇有關[多個 NIC 的 VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) 的更詳盡文章可逐步引導您建立網路安全性群組並指派 NIC。

## 建立虛擬機器
現在開始建置您的 VM 組態。在每個 VM 大小中，您可以新增至 VM 的 NIC 總數是有限制的。深入了解 [Windows VM 大小](virtual-machines-windows-sizes.md)。下列範例使用最多支援兩個 NIC 的 VM 大小 (`Standard_DS2_v2`)：

```powershell
$cred = Get-Credential

$vmConfig = New-AzureRmVMConfig -VMName TestVM -VMSize "Standard_DS2_v2"

$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName TestVM `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
```

連接您先前建立的兩個 NIC：

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC2.Id
```

針對新的 VM 設定儲存體和虛擬磁碟：

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption fromImage
```

最後，建立 VM：

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName TestRG -Location WestUS
```

## 使用 Resource Manager 範本建立多個 NIC
Azure Resource Manager 範本會使用宣告式 JSON 檔案來定義您的環境。您可以閱讀 [Azure Resource Manager 概觀](../resource-group-overview.md)。Resource Manager 範本提供一種方式，可在部署期間建立資源的多個執行個體，例如建立多個 NIC。您使用 *copy* 來指定要建立的執行個體數目：

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

深入了解[使用 *copy* 建立多個執行個體](../resource-group-create-multiple.md)。

您也可以使用 `copyIndex()`，然後在資源名稱後面附加一個數字，讓您能夠建立 `NIC1`、`NIC2`，依此類推。以下顯示附加索引值的範例：

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

您可以閱讀[使用 Resource Manager 範本建立多個 NIC](../virtual-network/virtual-network-deploy-multinic-arm-template.md) 的完整範例。

## 後續步驟
嘗試建立具有多個 NIC 的 VM 時，請務必檢閱 [Windows VM 大小](virtual-machines-windows-sizes.md)。注意每個 VM 大小所支援的 NIC 數目上限。

請記住，您無法在現有 VM 中新增其他 NIC，您必須在部署 VM 時建立所有 NIC。小心規劃您的部署，以確定您一開始就會有所有需要的網路連線。

<!---HONumber=AcomDC_0817_2016-->