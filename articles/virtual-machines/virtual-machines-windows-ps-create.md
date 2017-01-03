---
title: "使用 Azure Powershell 建立 Azure VM | Microsoft Docs"
description: "使用 Azure PowerShell 和 Azure Resource Manager，輕鬆建立執行 Windows Server 的新 VM。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 819b40302f158d1d6224878c164cf7ff71947887


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>使用 Resource Manager 和 PowerShell 建立 Windows VM
本文將說明如何使用 [Resource Manager](../azure-resource-manager/resource-group-overview.md) 和 PowerShell 快速建立執行 Windows Server 的 Azure 虛擬機器及其所需的資源。 

必須執行本文中的所有步驟才能建立虛擬機器，而且應該需要約 30 分鐘的時間才能完成步驟。 將命令中的範例參數值取代為對您環境有意義的名稱。

## <a name="step-1-install-azure-powershell"></a>步驟 1：安裝 Azure PowerShell
如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="step-2-create-a-resource-group"></a>步驟 2：建立資源群組
所有資源都必須包含在資源群組中，因此就讓我們先建立。  

1. 取得可以建立資源的可用位置清單。
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```
2. 設定資源的位置。 此命令會將位置設定為 **centralus**。
   
    ```powershell
    $location = "centralus"
    ```
3. 建立資源群組。 此命令會在您設定的位置中建立名為 **myResourceGroup** 的資源群組。
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-create-a-storage-account"></a>步驟 3：建立儲存體帳戶
儲存您建立的虛擬機器所使用的虛擬硬碟時，需要 [儲存體帳戶](../storage/storage-introduction.md) 。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。

1. 測試儲存體帳戶名稱的唯一性。 此命令會測試名稱 **myStorageAccount**。
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    如果這個命令傳回 **True**，表示您提出的名稱是 Azure 內唯一的。 
2. 現在，建立儲存體帳戶。
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>步驟 4：建立虛擬網路
所有虛擬機器都屬於 [虛擬網路](../virtual-network/virtual-networks-overview.md)的一部分。

1. 建立虛擬網路的子網路。 此命令會建立名為 **mySubnet** 且具有位址首碼 10.0.0.0/24 的子網路。
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
2. 現在，建立虛擬網路。 此命令會使用您建立的子網路以及位址首碼 **10.0.0.0/16**，來建立名為 **myVnet** 的虛擬網路。
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>步驟 5︰建立公用 IP 位址和網路介面
若要能夠與虛擬網路中的虛擬機器進行通訊，您需要 [公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和網路介面。

1. 建立公用 IP 位址。 此命令會以 **Dynamic** 的配置方法來建立名為 **myPublicIp** 的公用 IP 位址。
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. 建立網路介面。 此命令會建立名為 **myNIC** 的網路介面。
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>步驟 6：建立虛擬機器
您已備妥所有項目，現在可以開始建立虛擬機器。

1. 執行此命令來設定虛擬機器的系統管理員帳戶名稱和密碼。

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    密碼長度必須介於 12-123 個字元，並且具有至少 1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元。 
2. 為虛擬機器建立組態物件。 此命令會建立名為 **myVmConfig** 的組態物件，定義 VM 的名稱與 VM 的大小。
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    如需虛擬機器的可用大小清單，請參閱 [Azure 中的虛擬機器大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 。
3. 設定 VM 的作業系統設定。 此命令會設定 VM 的電腦名稱、作業系統類型和帳戶認證。
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```
4. 定義要用來佈建 VM 的映像。 此命令會定義要用於 VM 的 Windows Server 映像。 
   
    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
   
    如需有關選取要使用之映像的詳細資訊，請參閱[使用 PowerShell 或 CLI 在 Azure 中瀏覽和選取 Windows 虛擬機器映像](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
5. 將您所建立的網路介面新增至組態。
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. 定義 VM 硬碟的名稱和位置。 虛擬硬碟檔案會儲存在容器中。 此命令會在您所建立的儲存體帳戶中，在名為 **vhds/myOsDisk1.vhd** 的容器中建立磁碟。
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. 將作業系統磁碟資訊新增至 VM 組態。 使用作業系統磁碟的名稱取代 **$diskName** 的值。 建立變數並將磁碟資訊新增至組態。
   
    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. 最後，建立虛擬機器。
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>後續步驟
* 如果部署有問題，下一個步驟就是查看 [使用 Azure 入口網站針對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)
* 檢閱[使用 Azure Resource Manager 和 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，以了解如何管理您建立的虛擬機器。
* 使用 [利用 Resource Manager 範本建立 Windows 虛擬機器](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Dec16_HO2-->


