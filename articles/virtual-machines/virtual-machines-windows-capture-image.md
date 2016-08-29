<properties
	pageTitle="從 Azure VM 建立 VM 映像 | Microsoft Azure"
	description="了解如何從 Resource Manager 部署模型中建立的現有 Azure VM 建立一般化 VM 映像"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="cynthn"/>

# 如何從現有的 Azure VM 建立 VM 映像


本文說明如何使用 Azure PowerShell 建立現有 Azure VM的一般化映像。然後可以使用映像來建立另一個 VM。此映像包含作業系統磁碟與連結到虛擬機器的資料磁碟。映像不包含虛擬網路資源，因此您需要在使用映像建立 VM 時設定這些資源。此程序會建立[一般化 Windows 映像](https://technet.microsoft.com/library/hh824938.aspx)。


## 必要條件

- 這些步驟假設您在 Resource Manager 部署模型中已經有要用來建立映像的 Azure 虛擬機器。您需要 VM 名稱和資源群組名稱。您可以輸入 PowerShell Cmdlet `Get-AzureRmResourceGroup`，以取得訂用帳戶中的資源群組清單。您也可以輸入 `Get-AzureRMVM`，以取得訂用帳戶的 VM 清單。

- 您需要安裝 Azure PowerShell 1.0.x 版。如果您尚未安裝 PowerShell，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 以了解安裝步驟。

- 請確定 Sysprep 支援電腦上執行的伺服器角色。如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## 準備來源 VM 

本節說明如何將您的 Windows 虛擬機器一般化，以便當作映像使用。

> [AZURE.WARNING] VM 一般化後，您就無法透過 RDP 登入該 VM，因為程序會移除所有的使用者帳戶。無法回復變更。

1. 請登入 Windows 虛擬機器。在 [Azure 入口網站](https://portal.azure.com)中，依序前往 [瀏覽] > [虛擬機器] > 您的 Windows 虛擬機器 > [連線]。

2. 以系統管理員身分開啟 [命令提示字元] 視窗。

3. 切換至 `%windir%\system32\sysprep` 目錄，然後執行 sysprep.exe。

4. 在 [系統準備工具] 對話方塊中，執行下列動作：

	- 在 [系統清理動作] 中選取 [Enter System Out-of-Box Experience (OOBE)]，並確認 [一般化] 已勾選。如需 Sysprep 的詳細用法，請參閱[如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

	- 在 [關機選項]中選取 [關機]。

	- 按一下 [確定]。

	![執行 Sysprep](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   Sysprep 會關閉虛擬機器。在 Azure 入口網站中，該虛擬機器的狀態會變更成 [已停止]。


## 登入 Azure PowerShell

1. 開啟 Azure PowerShell，並登入您的 Azure 帳戶。

		Login-AzureRmAccount

	這會開啟一個可供您輸入 Azure 帳戶認證的快顯視窗。

2. 取得您可用訂用帳戶的訂用帳戶識別碼。

		Get-AzureRmSubscription

3. 使用訂用帳戶識別碼來設定正確的訂用帳戶。

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"


## 解除配置 VM 並將狀態設定為一般化		

1. 解除配置 VM 資源。

		Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>

	Azure 入口網站中 VM 的「狀態」會從 [已停止] 變更為 [已停止 (已解除配置)]。

2. 將虛擬機器的狀態設定為 [一般化]。

		Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized

3. 檢查 VM 的狀態。VM 的 [OSState/一般化] 區段中的 [DisplayStatus] 應設定為 [VM 一般化]。
		
		$vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -status
		$vm.Statuses

		
## 建立映像 

1. 使用這個命令，將虛擬機器映像複製到目的地儲存體容器中。此映像會建立在與原始虛擬機器相同的儲存體帳戶中。`-Path` 變數會在本機儲存 JSON 範本的複本。`-DestinationContainerName` 變數是要用以保存映像的容器名稱。如果此容器不存在，則會為您建立。

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -Name YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	您可以從 JSON 檔案範本取得映像的 URL。請依序前往 [資源] > [storageProfile] > [osDisk] > [映像] > [uri] 區段，取得您映像的完整路徑。映像的 URL 如下所示：`https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
	
	您也可以在入口網站中驗證 URI。系統會將映像複製到儲存體帳戶中名為 **system** 的 Blob。

2. 建立映像路徑的變數。

		$imageURI = "<https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd>"


## 建立虛擬網路

建立[虛擬網路](../virtual-network/virtual-networks-overview.md)的 vNet 和 subNet。
		

1. 使用您自己的資訊來取代變數的值。以 CIDR 格式提供子網路的位址首碼。建立變數和子網路。

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. 使用虛擬網路的名稱來取代 **$vnetName** 的值。以 CIDR 格式提供虛擬網路的位址首碼。建立變數和具有子網路的虛擬網路。

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## 建立公用 IP 位址和網路介面

若要能夠與虛擬網路中的虛擬機器進行通訊，您需要[公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)和網路介面。

1. 使用公用 IP 位址的名稱來取代 **$ipName** 的值。建立變數和公用 IP 位址。

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. 使用網路介面的名稱來取代 **$nicName** 的值。建立變數和網路介面。

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


## 建立 VM

下列 PowerShell 指令碼示範如何設定虛擬機器組態，以及使用已上傳的 VM 映像做為新安裝的來源。

>[AZURE.NOTE] VM 必須位於與原始 VHD 相同的儲存體帳戶中。

</br>

	
	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account 
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"
	
	# Assign a SKU name
	# Valid values for -SkuName are: **Standard_LRS** - locally redundant storage, **Standard_ZRS** - zone redundant storage, **Standard_GRS** - geo redundant storage, **Standard_RAGRS** - read access geo redundant storage, **Premium_LRS** - premium locally redundant storage. 
	$skuName = "<skuName>"
	
	# Create a new storage account for the VM
	New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $storageAccName -Location $location -SkuName $skuName -Kind "Storage"

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You set this variable when you uploaded the VHD
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $imageURI -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



完成時，在 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 底下，或是使用下列 PowerShell 命令，應該就可以看到新建立的 VM：

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 後續步驟

若要使用 Azure PowerShell 管理新的虛擬機器，請參閱[使用 Azure Resource Manager 與 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)。

<!---HONumber=AcomDC_0817_2016-->