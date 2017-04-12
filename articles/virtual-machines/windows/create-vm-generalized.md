---
title: "從一般化 VHD 建立 VM | Microsoft Docs"
description: "了解如何使用 Azure PowerShell，從儲存體帳戶中的一般化 VHD 映像建立 Windows 虛擬機器。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: b4808871-9ef1-49ea-a617-9154d417abb0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cad7bf8030bd60c6adc5fb0c9ad39a2d703face
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-vm-from-a-generalized-vhd-image-in-a-storage-account"></a>從儲存體帳戶中的一般化 VHD 映像建立 VM 

本主題將說明如何從儲存體帳戶中的一般化非受控磁碟建立 VM。 一般化 VHD 映像已使用 [Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 移除您所有的個人帳戶資訊。 您可以藉由在內部部署 VM 上執行 Sysprep，然後[將 VHD 上傳至 Azure](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，或藉由在現有 Azure VM 上執行 Sysprep，然後[複製 VHD](vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，以建立一般化 VHD。

如果您想要從儲存體帳戶中的特殊化 VHD 建立 VM，請參閱[從特殊化 VHD 建立 VM](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如需使用儲存體帳戶中的受控磁碟而非磁碟的相關資訊，請參閱[建立受控 VM 映像](capture-image-resource.md)和[從受控映像建立 VM](create-vm-generalized-managed.md)。

## <a name="prerequisites"></a>必要條件
如果您要使用從內部部署 VM 上傳的 VHD (例如使用 Hyper-V 建立的 VHD)，您應該確定您已遵循[準備要上傳至 Azure 的 Windows VHD](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 中的指示。 

您必須先將上傳的 VHD 和現有 Azure VM VHD 一般化，然後才可以使用此方法建立 VM。 如需詳細資訊，請參閱[使用 Sysprep 將 Windows 虛擬機器一般化](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

## <a name="set-the-uri-of-the-vhd"></a>設定 VHD 的 URI

要使用之 VHD 的 URI 格式如下：https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd。 在此範例中，名為 **myVHD** 的 VHD 位於容器 **mycontainer** 中的儲存體帳戶 **mystorageaccount**。

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>建立虛擬網路
建立[虛擬網路](../../virtual-network/virtual-networks-overview.md)的 vNet 和子網路。

1. 建立子網路。 下列範例會在資源群組 **myResourceGroup** 中建立名為 **mySubnet** 的子網路，而其位址首碼為 **10.0.0.0/24**。  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 建立虛擬網路 下列範例會在**美國西部**位置中建立名為 **myVnet** 的虛擬網路，其位址首碼為 **10.0.0.0/16**。  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>建立公用 IP 位址和網路介面
若要能夠與虛擬網路中的虛擬機器進行通訊，您需要 [公用 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和網路介面。

1. 建立公用 IP 位址。 此範例會建立名為 **myPip** 的公用 IP 位址。 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. 建立 NIC。 此範例會建立名為 **myNic** 的 NIC。 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>建立網路安全性群組和 RDP 規則
若要能夠使用 RDP 登入 VM，您必須有可在連接埠 3389 上允許 RDP 存取的安全性規則。 

此範例會建立名為 **myNsg** 的 NSG，其包含的規則 **myRdpRule** 可允許透過連接埠 3389 的 RDP 流量。 如需 NSG 的詳細資訊，請參閱[使用 PowerShell 對 Azure 中的 VM 開啟連接埠](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>建立虛擬網路的變數
為已完成的虛擬網路建立變數。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>建立 VM
下列 PowerShell 指令碼示範如何設定虛擬機器組態，以及使用已上傳的 VM 映像做為新安裝的來源。

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>確認已建立 VM
完成時，在 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 底下，或是使用下列 PowerShell 命令，應該就可以看到新建立的 VM：

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>後續步驟
若要使用 Azure PowerShell 管理新的虛擬機器，請參閱 [使用 Azure Resource Manager 與 PowerShell 管理虛擬機器](ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


