---
title: "Azure 中 Windows VM 的可用性設定組教學課程 | Microsoft Docs"
description: "了解 Azure 中 Windows VM 的可用性設定組。"
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>如何使用可用性設定組

在本教學課程中，您將了解如何藉由將虛擬機器 (VM) 放入名為可用性設定組的邏輯群組，來提高它們的可用性。 當您在可用性設定組中建立 VM 時，Azure 平台會將 VM 分散到基礎結構上。 如果發生硬體故障或平台上預定進行的維修作業，使用可用性設定組可確保至少會有一部 VM 仍然維持執行。

您可以使用最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) 模組來完成本教學課程中的步驟。

## <a name="availability-set-overview"></a>可用性設定組概觀

您可以在基礎的 Azure 資料中心內，跨硬體邏輯群組建立虛擬機器。 當您建立兩部或多部 VM 時，您的計算和儲存體資源就會分散於這些硬體上，例如伺服器、網路交換器及儲存體。 萬一有硬體元件正在進行維護，此分佈會維護您應用程式的可用性。 可用性設定組可讓您定義這個邏輯群組。

可用性設定組可為 VM 提供高可用性。 此外，您還應該確定您的應用程式設計也容許中斷或維護事件。

## <a name="create-an-availability-set"></a>建立可用性設定組

您可以使用 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 建立可用性設定組。 在此範例中，我們將針對 *myResourceGroupAvailability* 資源群組中名為 *myAvailabilitySet* 的可用性設定組，同時將更新數目和容錯網域數目設為 *2*。


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>建立位於可用性設定組內的 VM

VM 需要建立於可用性設定組內，以確保其會正確地分散到硬體上。 您無法在建立可用性設定組之後，將現有的 VM 加入至其中。 

位置中的硬體已分為多個更新網域和容錯網域。 **更新網域**是一組虛擬機器和可同時重新啟動的基礎實體硬體。 相同**容錯網域**中的 VM 會共用一般儲存體以及通用電源和網路交換器。 

當您透過 [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 使用組態建立 VM 時，會使用 `-AvailabilitySetId`參數來指定可用性設定組，以指定可用性設定組的識別碼。

在可用性設定組中使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 建立兩部 VM。

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

建立及設定這兩部 VM 需要幾分鐘的時間。 完成後，您將有 2 部分散於基礎硬體上的虛擬機器。 

## <a name="check-for-available-vm-sizes"></a>檢查可用的 VM 大小 

您稍後可以將更多 VM 加入至可用性設定組，但是需要知道硬體上有哪些可用的 VM 大小。 針對可用性設定組，使用 [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 來列出硬體叢集上所有可用的大小。

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立虛擬機器擴展集。 請前進到下一個教學課程，以了解虛擬機器擴展集。

[建立 VM 擴展集](tutorial-create-vmss.md)



