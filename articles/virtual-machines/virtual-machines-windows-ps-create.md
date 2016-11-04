---
title: 使用 Azure Powershell 建立 Azure VM | Microsoft Docs
description: 使用 Azure PowerShell 和 Azure Resource Manager，輕鬆建立執行 Windows Server 的新 VM。
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: davidmu

---
# 使用 Resource Manager 和 PowerShell 建立 Windows VM
本文將說明如何使用 [Resource Manager](../resource-group-overview.md) 和 PowerShell 快速建立執行 Windows Server 的 Azure 虛擬機器及其所需的資源。

必須執行本文中的所有步驟才能建立虛擬機器，而且應該需要約 30 分鐘的時間才能完成步驟。

## 步驟 1：安裝 Azure PowerShell
如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## 步驟 2：建立資源群組
首先，您要建立資源群組。

1. 取得可以建立資源的可用位置清單。
   
        Get-AzureRmLocation | sort Location | Select Location
   
    您應該會看到如下列範例的結果：
   
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus
2. 使用清單中的位置取代 **$locName** 的值。建立變數。
   
        $locName = "centralus"
3. 使用新資源群組的名稱取代 **$rgName** 的值。建立變數和資源群組。
   
        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

## 步驟 3：建立儲存體帳戶
儲存您建立的虛擬機器所使用的虛擬硬碟時，需要[儲存體帳戶](../storage/storage-introduction.md)。

1. 使用儲存體帳戶的名稱取代 **$stName** 的值。測試名稱的唯一性。
   
        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName
   
    如果這個命令傳回 **True**，表示您提出的名稱是 Azure 內唯一的。儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。
2. 現在，請執行以下命令來建立儲存體帳戶。
   
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName

## 步驟 4：建立虛擬網路
所有虛擬機器都屬於[虛擬網路](../virtual-network/virtual-networks-overview.md)的一部分。

1. 使用子網路的名稱取代 **$subnetName** 的值。建立變數和子網路。
   
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
2. 使用虛擬網路的名稱取代 **$vnetName** 的值。建立變數和具有子網路的虛擬網路。
   
        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
   
    使用對您的應用程式和環境有意義的值。

## 步驟 5︰建立公用 IP 位址和網路介面
若要與虛擬網路中的虛擬機器通訊，您需要[公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)和網路介面。

1. 使用公用 IP 位址的名稱來取代 **$ipName** 的值。建立變數和公用 IP 位址。
   
        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
2. 使用網路介面的名稱來取代 **$nicName** 的值。建立變數和網路介面。
   
        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

## 步驟 6：建立虛擬機器
您已備妥所有項目，現在可以開始建立虛擬機器。

1. 執行以下命令來設定虛擬機器的系統管理員帳戶名稱和密碼。
   
        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
   
    密碼長度必須介於 12-123 個字元，並且具有至少 1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元。
2. 使用虛擬機器的名稱取代 **$vmName** 的值。建立變數和虛擬機器組態。
   
        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
   
    如需虛擬機器的可用大小清單，請參閱 [Azure 中的虛擬機器大小](virtual-machines-windows-sizes.md)。
3. 使用虛擬機器的電腦名稱取代 **$compName** 的值。建立變數並將作業系統資訊新增至組態。
   
        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
4. 定義要用來佈建虛擬機器的映像。
   
        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
   
    如需有關選取要使用之映像的詳細資訊，請參閱[使用 PowerShell 或 CLI 在 Azure 中瀏覽和選取 Windows 虛擬機器映像](virtual-machines-windows-cli-ps-findimage.md)。
5. 將您所建立的網路介面新增至組態。
   
        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
6. 使用虛擬硬碟儲存體中的路徑和檔案名稱取代 **$blobPath** 的值。虛擬硬碟檔案通常會儲存在容器中，例如 **vhds/WindowsVMosDisk.vhd**。建立變數。
   
        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
7. 使用作業系統磁碟的名稱取代 **$diskName** 的值。建立變數並將磁碟資訊新增至組態。
   
        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
8. 最後，建立虛擬機器。
   
        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm
   
    您應該會在 Azure 入口網站中看見資源群組與其所有資源，而在 PowerShell 視窗中看見成功狀態︰
   
        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## 後續步驟
* 如果部署有問題，下一個步驟就是查看[使用 Azure 入口網站針對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)
* 檢閱[使用 Azure Resource Manager 和 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)，以了解如何管理您建立的虛擬機器。
* 使用[利用 Resource Manager 範本建立 Windows 虛擬機器](virtual-machines-windows-ps-template.md)中的資訊，充分運用使用範本建立虛擬機器。

<!---HONumber=AcomDC_1005_2016-->