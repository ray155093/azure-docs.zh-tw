---
title: "在 Azure 中從特製化磁碟建立 VM | Microsoft Docs"
description: "藉由附加特製化受控磁碟或非受控磁碟，在 Resource Manager 部署模型中建立新的 VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: cbe3d72bbd0d9cc425b1b26ad412e77b33f385b2
ms.lasthandoff: 02/11/2017


---
# <a name="create-a-vm-from-a-specialized-disk"></a>從特殊化磁碟建立 VM

藉由使用 Powershell 附加特製化磁碟作為 OS 磁碟來建立新的 VM。 特製化磁碟是現有 VM 中的 VHD 複本，可從原始的 VM 維護使用者帳戶、應用程式和其他狀態資料。 您可以使用特製化[受控磁碟](../storage/storage-managed-disks-overview.md)或特製化非受控磁碟來建立新的 VM。

## <a name="before-you-begin"></a>開始之前
如果您使用 PowerShell，請確定您擁有最新版的 AzureRM.Compute PowerShell 模組。 執行下列命令來安裝它。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)。


## <a name="create-the-subnet-and-vnet"></a>建立子網路和 VNet

建立 [虛擬網路](../virtual-network/virtual-networks-overview.md)的 vNet 和 subNet。

1. 建立子網路。 此範例會在資源群組 **myResourceGroup** 中建立名為 **mySubnet** 的子網路，並將子網路位址首碼設定為 **10.0.0.0/24**。
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 建立 vNet。 此範例會將虛擬網路名稱設定為 **myVnetName**、位置設定為 **美國西部**，及虛擬網路的位址首碼設定為 **10.0.0.0/16**。 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>建立公用 IP 位址和 NIC
若要能夠與虛擬網路中的虛擬機器進行通訊，您需要 [公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和網路介面。

1. 建立公用 IP。 在此範例中，公用 IP 位址名稱會設定為 **myIP**。
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. 建立 NIC。 在此範例中，NIC 名稱會設定為 **myNicName**。
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>建立網路安全性群組和 RDP 規則
若要能夠使用 RDP 登入 VM，您必須有可在連接埠 3389 上允許 RDP 存取的安全性規則。 因為新 VM 的 VHD 是從現有的特製化 VM 所建立，在建立 VM 之後，您可以從具有使用 RDP 之登入權限的來源虛擬機器使用現有帳戶。

此範例會將 NSG 名稱設定為 **myNsg** 以及將 RDP 規則名稱設定為 **myRdpRule**。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

如需端點和 NSG 規則的詳細資訊，請參閱[使用 PowerShell 對 Azure 中的 VM 開啟連接埠](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="set-the-vm-name-and-size"></a>設定 VM 名稱和大小

此範例將 VM 名稱設定為 "myVM"，將 VM 大小設定為 "Standard_A2"。

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>新增 NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>設定 OS 磁碟

特製化 OS 可以是您[上傳至 Azure](virtual-machines-windows-upload-image.md) 的 VHD，或[現有 Azure VM 中的 VHD 複本](virtual-machines-windows-vhd-copy.md)。 

您有兩個選項可選擇：
- **選項 1**︰從現有儲存體帳戶中的特製化 VHD 建立特製化受控磁碟，作為 OS 磁碟。

或 

- **選項 2**︰使用您自己的儲存體帳戶中儲存的特製化 VHD (非受控磁碟)。 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>選項 1︰從非受控的特製化磁碟建立受控磁碟

1. 從儲存體帳戶中現有的特製化 VHD 建立受控磁碟。 這個範例使用 **myOSDisk1** 作為磁碟名稱，將磁碟放入 **StandardLRS** 儲存體，並使用 **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** 作為來源 VHD 的 URI。

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. 將 OS 磁碟新增至組態。 這個範例將磁碟大小設定為 **128GB**，並附加受控磁碟作為 **Windows** OS 磁碟。
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

選擇性︰附加其他受控磁碟作為資料磁碟。 此選項假設您已依照[建立受控資料磁碟](virtual-machines-windows-create-managed-disk-ps.md)來建立受控資料磁碟。 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>選項 2︰附加現有儲存體帳戶中的 VHD

1. 為您想要使用的 VHD 設定 URI。 在此範例中，VHD 檔案 **myOsDisk.vhd** 保存在容器 **myContainer** 中的儲存體帳戶 **myStorageAccount**。

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. 使用所複製 OS VHD 的 URL 新增 OS 磁碟。 在此範例中，建立 OS 磁碟時，"osDisk" 一詞會附加至 VM 名稱而形成 OS 磁碟名稱。 這個範例也指定這個以 Windows 為基礎的 VHD，應該附加至 VM 作為 OS 磁碟。
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

選擇性︰如果您有資料磁碟需要附加至 VM，請使用資料 VHD 的 URL 和適當的邏輯單元編號 (Lun) 新增資料磁碟。

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

使用儲存體帳戶時，資料和作業系統磁碟的 URL 近似於︰`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`。 您可以藉由下列方法在入口網站上找到它：瀏覽至目標儲存體容器，按一下複製的作業系統或資料 VHD，然後複製 URL 的內容。


## <a name="create-the-vm"></a>建立 VM

使用我們剛才建立的組態建立 VM。

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

如果此命令成功，您會看到如下的輸出︰

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>確認已建立 VM
從 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器]下，或是使用下列 PowerShell 命令，應可看到新建立的 VM：

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>後續步驟
若要登入新的虛擬機器，請瀏覽至[入口網站](https://portal.azure.com)中的 VM，按一下 [連接] 並開啟遠端桌面 RDP 檔案。 使用原始虛擬機器的帳戶認證來登入新的虛擬機器。 如需詳細資訊，請參閱 [如何連接和登入執行 Windows 的 Azure 虛擬機器](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


