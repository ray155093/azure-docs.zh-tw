---
title: "使用 PowerShell 建立 Azure 虛擬機器擴展集 | Microsoft Docs"
description: "使用 PowerShell 建立 Azure 虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 1f8e66fac5b82698525794f0486dd0432c7421a7
ms.openlocfilehash: 7286fed39839675eb960b749f3235f83e36c5e9a
ms.lasthandoff: 02/22/2017


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>使用 Azure PowerShell 建立 Windows 虛擬機器擴展集
這些步驟會遵循填空方法建立 Azure 虛擬機器擴展集。 若要深入了解擴展集，請參閱 [虛擬機器擴展集概觀](virtual-machine-scale-sets-overview.md) 。

執行本文中的步驟應該大約 30 分鐘的時間。

## <a name="step-1-install-azure-powershell"></a>步驟 1：安裝 Azure PowerShell
如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="step-2-create-resources"></a>步驟 2：建立資源
建立新的擴展集所需要的資源。

### <a name="resource-group"></a>資源群組
虛擬機器擴展集必須包含在資源群組中。

1. 取得您可以放置資源的可用位置清單：
   
        Get-AzureLocation | Sort Name | Select Name
2. 挑選最適合您的位置，使用該位置的名稱取代 **$locName** 的值，然後建立變數︰
   
        $locName = "location name from the list, such as Central US"
3. 以您要用於新資源群組的名稱取代 **$rgName** 的值，然後建立變數︰ 
   
        $rgName = "resource group name"
4. 建立資源群組：
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    您應該會看到如下列範例的結果：
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="virtual-network"></a>虛擬網路
擴展集中的虛擬機器需要虛擬網路。

1. 以您要用於虛擬網路子網路的名稱取代 **$subnetName** 的值，然後建立變數： 
   
        $subnetName = "subnet name"
2. 建立子網路組態：
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    您的虛擬網路的位址前置詞可能不同。
3. 以您要用於虛擬網路的名稱取代 **$netName** 的值，然後建立變數︰ 
   
        $netName = "virtual network name"
4. 建立虛擬網路：
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>擴展集的組態
您具備擴展集組態所需的所有資源，讓我們開始建立。  

1. 以您要用於 IP 組態的名稱取代 **$ipName** 的值，然後建立變數︰ 
   
        $ipName = "IP configuration name"
2. 建立 IP 組態：
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. 以您要用於擴展集組態的名稱取代 **$vmssConfig** 的值，然後建立變數︰   
   
        $vmssConfig = "Scale set configuration name"
4. 建立擴展集的組態：
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    此範例顯示使用三部虛擬機器建立的擴展集。 若要深入了解擴展集的容量，請參閱 [虛擬機器擴展集概觀](virtual-machine-scale-sets-overview.md) 。 這個步驟也包括設定集合中的虛擬機器大小 (稱為 SkuName)。 若要尋找符合您需求的大小，請參閱[虛擬機器大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
5. 將網路介面組態新增至擴展集組態：
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    您應該會看到如下列範例的結果：
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>作業系統設定檔
1. 以您要使用的電腦名稱前置詞名稱取代 **$computerName** 的值，然後建立變數︰ 
   
        $computerName = "computer name prefix"
2. 以虛擬機器的系統管理員帳戶的名稱取代 **$adminName** 的值，然後建立變數：
   
        $adminName = "administrator account name"
3. 以帳戶密碼取代 **$adminPassword** 的值，然後建立變數︰
   
        $adminPassword = "password for administrator accounts"
4. 建立作業系統設定檔：
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>儲存體設定檔
1. 以您要用於儲存體設定檔的名稱取代 **$storageProfile** 的值，然後建立變數︰  
   
        $storageProfile = "storage profile name"
2. 建立變數，該變數定義要使用的映像︰  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    若要尋找要使用的其他映像的資訊，請參閱[使用 Windows PowerShell 和 Azure CLI 巡覽和選取 Azure 虛擬機器映像](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

3. 建立儲存體設定檔：
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>虛擬機器擴展集
最後，您可以建立擴展集。

1. 以虛擬機器擴展集的名稱取代 **$vmssName** 的值，然後建立變數︰
   
        $vmssName = "scale set name"
2. 建立擴展集：
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    您應該會看到如下列範例的結果，顯示部署成功：
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>步驟 3︰瀏覽資源
請使用這些資源瀏覽您建立的虛擬機器擴展集：

* Azure 入口網站 - 使用入口網站可以取得的有限資訊。
* [Azure 資源總管](https://resources.azure.com/) - 此工具是瀏覽擴展集目前狀態的最佳選項。
* Azure PowerShell - 使用這個命令取得資訊：
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>後續步驟
* 使用 [管理虛擬機器擴展集中的虛擬機器](virtual-machine-scale-sets-windows-manage.md)
* 請考慮使用 [自動調整與虛擬機器擴展集](virtual-machine-scale-sets-autoscale-overview.md)
* 檢閱 [使用虛擬機器擴展集垂直自動調整](virtual-machine-scale-sets-vertical-scale-reprovision.md)


