---
title: "使用 Azure PowerShell 管理 Windows 虛擬機器 | Microsoft Docs"
description: "教學課程 - 使用 Azure PowerShell 管理 Windows 虛擬機器"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 8dde92d3bee451ee701508cec98b3581384fd16c
ms.lasthandoff: 04/06/2017

---

# <a name="manage-windows-virtual-machines-with-azure-powershell"></a>使用 Azure PowerShell 管理 Windows 虛擬機器

在本教學課程中，您將建立虛擬機器並執行一般管理工作，例如新增磁碟、自動化軟體安裝、管理防火牆規則，以及建立虛擬機器快照集。

若要完成本教學課程，請確定您已安裝最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) 模組。

## <a name="step-1--log-in-to-azure"></a>步驟 1 - 登入 Azure

首先，使用 Login-AzureRmAccount 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```

## <a name="step-2---create-resource-group"></a>步驟 2 - 建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 資源群組必須在虛擬機器之前建立。

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v2.0.3/new-azurermresourcegroup)建立資源群組。  在此範例中，會在`westeurope`區域中建立名為 `myResourceGroup` 的資源群組：

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-3---create-virtual-machine"></a>步驟 3 - 建立虛擬機器

虛擬機器必須連線到虛擬網路。 您使用公用 IP 位址透過網路介面卡與虛擬機器通訊。

### <a name="create-virtual-network"></a>建立虛擬網路

使用 [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermvirtualnetworksubnetconfig) 建立子網路：

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24
```

使用 [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermvirtualnetwork) 建立虛擬網路：

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>建立公用 IP 位址

使用 [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermpublicipaddress) 建立公用 IP 位址：

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>建立網路介面卡

使用 [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermnetworkinterface) 建立網路介面卡：

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>建立網路安全性群組

Azure [網路安全性群組](../virtual-network/virtual-networks-nsg.md) (NSG) 控制一或多部虛擬機器的輸入和傳出流量。 網路安全性群組規則可允許或拒絕特定連接埠或連接埠範圍上的網路流量。 這些規則也可以包含來源位址首碼，所以只有在預先定義的來源產生的流量可以與虛擬機器通訊。 若要存取您安裝的 IIS Web 伺服器，您必須新增輸入 NSG 規則。

若要建立輸入 NSG 規則，請使用 [Add-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/add-azurermnetworksecurityruleconfig)。 下列範例會建立名為 `myNSGRule` 的 NSG 規則，該規則會為虛擬機器開啟連接埠 `80`︰

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

使用 `myNSGRule` 與 [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermnetworksecuritygroup) 建立 NSG：

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

使用 [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/set-azurermvirtualnetworksubnetconfig) 在虛擬網路中將 NSG 新增至子網路：

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -Name mySubnet `
  -VirtualNetwork $vnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

使用 [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/set-azurermvirtualnetwork) 更新虛擬網路：

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Create virtual machine

建立虛擬機器時，有數個可用的選項，例如作業系統映像、磁碟大小及系統管理認證。 在此範例中，使用 `myVM` 名稱建立的虛擬機器會執行最新版本的 Windows Server 2016 Datacenter。

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 設定虛擬機器上系統管理員帳戶所需的使用者名稱和密碼：

```powershell
$cred = Get-Credential
```

使用 [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig) 建立虛擬機器的初始組態：

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

使用 [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem) 將作業系統資訊新增至虛擬機器組態：

```powershell
$vm = Set-AzureRmVMOperatingSystem -VM $vm `
  -Windows `
  -ComputerName myVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
```

使用 [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage) 將映像資訊新增至虛擬機器組態：

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest
```

使用 [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk) 將作業系統磁碟設定新增至虛擬機器組態：

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm `
  -Name myOsDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
```

使用 [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) 將您先前建立的網路介面卡新增至虛擬機器組態：

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

使用 [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvm) 建立虛擬機器：

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
```

## <a name="step-4--add-data-disk"></a>步驟 4 – 新增資料磁碟

根據預設，Azure 虛擬機器是使用單一作業系統磁碟建立。 可以為多磁碟儲存體組態、應用程式安裝與資料新增額外的磁碟。

### <a name="create-disk"></a>建立磁碟

使用 [New-AzureRmDiskConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdiskconfig) 建立資料磁碟的初始組態。 下列範例會建立名為 `myDataDisk` 的磁碟，其大小為 50 GB：

```powershell
$diskConfig = New-AzureRmDiskConfig `
  -Location westeurope `
  -CreateOption Empty `
  -DiskSizeGB 50
```

使用 [New-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdisk) 建立資料磁碟：

```powershell
$dataDisk = New-AzureRmDisk `
  -ResourceGroupName myResourceGroup `
  -DiskName myDataDisk `
  -Disk $diskConfig
```

使用 [Get-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermvm) 取得您想要在其中新增資料磁碟的虛擬機器：

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

使用 [Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmdatadisk) 將資料磁碟新增至虛擬機器組態：

```powershell
$vm = Add-AzureRmVMDataDisk `
  -VM $vm `
  -Name myDataDisk `
  -CreateOption Attach `
  -ManagedDiskId $dataDisk.Id `
  -Lun 1
```

使用 [Update-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/update-azurermvm) 更新虛擬機器：

```powershell
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="step-5--automate-configuration"></a>步驟 5 - 自動化組態

Azure 虛擬機器擴充功能是用來自動化虛擬機器組態工作，例如安裝應用程式和設定作業系統。 [適用於 Windows 的自訂指令碼擴充功能](./virtual-machines-windows-extensions-customscript.md)是用來在虛擬機器上執行任何 PowerShell 指令碼。 指令碼可以儲存在 Azure 儲存體、任何可存取的 HTTP 端點，或內嵌在自訂指令碼擴充功能組態。 在本教學課程中，兩個動作會自動化︰

- 安裝 IIS
- 格式化 VM 上的資料磁碟

因為擴充功能會在 VM 部署時執行，**install-iis-format-disk.ps1**檔案必須在建立虛擬機器之前定義。 針對本教學課程，檔案位於 Azure PowerShell 指令碼存放庫中。 此檔案包含 `Add-WindowsFeature Web-Server` 命令和 `Get-Disk | Where partitionstyle -eq 'raw' | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false**` 命令。

使用 [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmextension) 新增擴充功能：

```powershell
Set-AzureRmVMExtension -Name myScript `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -VMName myVM `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.4 `
  -SettingString '{"fileUris":["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/virtual-machine/install-iis-format-disk/install-iis-format-disk.ps1?token=ABlGkLjzVTdZGoCRbu1pCXjvSDRMHnUlks5Y5nbZwA%3D%3D"], "commandToExecute":"powershell.exe -ExecutionPolicy Unrestricted -File install-iis-format-disk.ps1" }'
```

使用 [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/get-azurermpublicipaddress) 取得虛擬機器的公用 IP 位址：

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIPAddress | select IpAddress
```

現在瀏覽至虛擬機器的公用 IP 位址。 備妥 NSG 規則，就會顯示預設 IIS 網站。


## <a name="step-6--snapshot-virtual-machine"></a>步驟 6 – 建立虛擬機器快照集

擷取虛擬機器的快照集會建立虛擬機器作業系統磁碟的唯讀、時間點複本。 透過快照集，即可將虛擬機器還原為特定狀態，或可將快照集用來建立具有相同狀態的新虛擬機器。

### <a name="create-snapshot"></a>建立快照集

在建立虛擬機器磁碟快照集之前，需要虛擬機器作業系統磁碟的 ID。

使用 [Get-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermdisk) 取得作業系統磁碟：

```powershell
$vmOSDisk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name myOSDisk
```

使用 New-AzureRmSnapshotConfig 建立快照集的組態：

```powershell
$snapshotConfig = New-AzureRmSnapshotConfig -Location westeurope -CreateOption Copy -SourceResourceId $vmOSDisk.id
```

使用 New-AzureRmSnapshot 建立快照集：

```powershell
$snapshot = New-AzureRmSnapshot -ResourceGroupName myResourceGroup -SnapshotName mySnapshot -Snapshot $snapshotConfig
```

### <a name="create-disk-from-snapshot"></a>從快照集建立磁碟

此快照集可以接著轉換成磁碟，進而用於重新建立虛擬機器。

使用 [New-AzureRmDiskConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdiskconfig) 建立磁碟的組態：

```powershell
$diskConfig = New-AzureRmDiskConfig -Location westeurope -CreateOption Copy -SourceResourceId $snapshot.id
```

使用 [New-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdisk) 建立磁碟：

```powershell
$disk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myOSDiskFromSnapshot -Disk $diskConfig
```

### <a name="restore-virtual-machine-from-snapshot"></a>從快照集還原虛擬機器

若要示範虛擬機器復原，請刪除現有的虛擬機器。

```powershell
Remove-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

從快照磁碟建立新的虛擬機器。 在本範例中，會指定現有的網路介面。 此組態會將所有先前建立的 NSG 規則套用至新的虛擬機器。

使用 [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig) 建立虛擬機器的初始組態：

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

使用 [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk) 新增作業系統磁碟：

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -CreateOption Attach -ManagedDiskId $disk.Id -Windows
```

使用 [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) 新增網路介面卡：

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

使用 [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvm) 建立虛擬機器：

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -Location westeurope
```

將虛擬機器的公用 IP 位址輸入到網際網路瀏覽器的網址列。 您應該會看到 IIS 正在還原的虛擬機器中執行。

## <a name="step-7--management-tasks"></a>步驟 7 – 管理工作

在虛擬機器的生命週期內，您可以執行一些管理工作，例如啟動、停止或刪除虛擬機器。 此外，您可以建立指令碼來自動執行重複或複雜的工作。 使用 Azure PowerShell，可以從命令列或在指令碼中執行許多常見的管理工作。

### <a name="stop-virtual-machine"></a>停止虛擬機器

使用 [Stop-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/stop-azurermvm) 停止及解除配置虛擬機器：

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

如果您想要將虛擬機器保留在佈建狀態，請使用 -StayProvisioned 參數。

### <a name="resize-virtual-machine"></a>調整虛擬機器的大小

若要調整已停止和已解除配置虛擬機器的大小，您需要所選 Azure 區域中可用的大小名稱。 使用 [Get-AzureRmVMSize](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermvmsize) 即可找到這些大小：

```powershell
Get-AzureRmVMSize -Location westeurope
```

使用 [Get-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermdisk) 取得您想要調整大小的虛擬機器：

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

設定虛擬機器的新大小，方法是使用您選取的大小來設定 vmSize 屬性。

```powershell
$vm.HardwareProfile.vmSize = "Standard_F4s"
```

使用 [Update-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/update-azurermvm) 更新虛擬機器：

```powershell
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

### <a name="start-virtual-machine"></a>啟動虛擬機器

```powershell
Start-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

### <a name="delete-resource-group"></a>刪除資源群組

刪除資源群組同時會刪除其內含的所有資源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

範例 – [Azure 虛擬機器 PowerShell 範例指令碼](./virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

