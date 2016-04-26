<properties
	pageTitle="使用 Azure Powershell 建立 Azure VM | Microsoft Azure"
	description="使用 Azure PowerShell 和 Azure Resource Manager，輕鬆建立執行 Windows Server 的新 VM。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/12/2016"
	ms.author="davidmu"/>

# 使用資源管理員和 PowerShell 建立 Windows VM

本文將說明如何使用 Resource Manager 和 PowerShell 快速建立執行 Windows Server 的 Azure 虛擬機器及其相關聯的資源。

執行本文中的步驟應該大約 30 分鐘的時間。

## 步驟 1：安裝 Azure PowerShell

有關如何安裝最新版 Azure PowerShell 的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)，選取您要使用的訂用帳戶，然後登入您的 Azure 帳戶。
        
## 步驟 2：建立資源群組

所有資源都必須部署在資源群組中。如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../resource-group-overview.md)

1. 取得可以建立資源的可用位置清單。

	    Get-AzureLocation | sort Name | Select Name

2. 使用清單中的位置 (例如**美國中部**) 取代 **$locName** 的值。建立變數。

        $locName = "location name"
        
3. 使用新資源群組的名稱取代 **$rgName** 的值。建立變數和資源群組。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## 步驟 3：建立儲存體帳戶

儲存與您建立的虛擬機器相關聯的虛擬硬碟時，需要儲存體帳戶。

1. 使用儲存體帳戶名稱取代 **$stName** 的值 (僅限小寫字母和數字)。測試名稱的唯一性。

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    如果這個命令傳回 **False**，表示您設定的名稱不重複。
    
2. 現在，請執行以下命令來建立儲存體帳戶。
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## 步驟 4：建立虛擬網路

所有虛擬機器必須與虛擬網路相關聯。

1. 使用子網路名稱取代 **$subnetName** 的值。建立變數和子網路。
    	
        $subnetName = "subnet name"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. 使用虛擬網路名稱取代 **$vnetName** 的值。建立變數和具有子網路的虛擬網路。

        $vnetName = "virtual network name"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
## 步驟 5︰建立公用 IP 位址和網路介面

若要與虛擬網路中的虛擬機器通訊，您需要公用 IP 位址和網路介面。

1. 使用公用 IP 位址的名稱取代 **$ipName** 的值。建立變數和公用 IP 位址。

        $ipName = "public IP address name"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. 使用網路介面名稱取代 **$nicName** 的值。建立變數和網路介面。

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## 步驟 6：建立虛擬機器

您已備妥所有項目，現在可以開始建立虛擬機器。

1. 執行以下命令來設定虛擬機器的系統管理員帳戶名稱和密碼。

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
2. 使用虛擬機器名稱取代 **$vmName** 的值。建立變數和虛擬機器組態。

        $vmName = "virtual machine name"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    如需虛擬機器的可用大小清單，請參閱 [Azure 中的虛擬機器大小](virtual-machines-windows-sizes.md)。
    
3. 使用虛擬機器的電腦名稱取代 **$compName** 的值。建立變數並將作業系統資訊新增至組態。

        $compName = "computer name"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. 定義要用來佈建虛擬機器的映像。

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    如需有關選取要使用之映像的詳細資訊，請參閱[使用 PowerShell 或 CLI 在 Azure 中瀏覽和選取 Windows 虛擬機器映像](virtual-machines-windows-cli-ps-findimage.md)。
        
5. 將您所建立的網路介面新增至組態。

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. 使用虛擬硬碟儲存體中的路徑和檔案名稱取代 **$blobPath** 的值。vhd 檔案通常會儲存在容器中，例如 "vhds/WindowsVMosDisk.vhd"。建立變數。

        $blobPath = "vhd path and file name"
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

- 如果部署有問題，下一個步驟是查看[透過 Azure 入口網站針對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)
- 請參閱[使用 Azure Resource Manager 和 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)，了解如何管理您剛才建立的虛擬機器。
- 使用[利用 Resource Manager 範本建立 Windows 虛擬機器](virtual-machines-windows-ps-template.md)中的資訊，充分運用使用範本建立虛擬機器

<!---HONumber=AcomDC_0420_2016-->