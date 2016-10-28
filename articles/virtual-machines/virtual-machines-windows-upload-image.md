<properties
	pageTitle="上傳 Resource Manager 的 Windows VHD | Microsoft Azure"
	description="了解如何上傳以 Windows 為基礎的虛擬機器映像，以搭配 Resource Manager 部署模型一起使用。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="cynthn"/>

# 將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用


本文說明如何建立及上傳 Windows 虛擬硬碟 (VHD)，以便快速建立 VM。如需 Azure 中磁碟和 VHD 的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](virtual-machines-linux-about-disks-vhds.md)。


## 必要條件

本文假設您擁有：

- **Azure 訂用帳戶** - 如果您尚未擁有 Azure 訂用帳戶，請[免費註冊 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F)，或[啟用 MSDN 啟用 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

- **Azure PowerShell 1.4 版或更高** - 如果尚未安裝，請參閱 [How to install and configure Azure PowerShell (如何安裝和設定 Azure PowerShell)](../powershell-install-configure.md)。

- **執行 Windows 的虛擬機器** - 有許多工具可讓您在內部部署建立虛擬機器。如需範例，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。如需有關 Azure 上支援哪些 Windows 作業系統的資訊，請參閱 [Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/kb/2721672)。

- 請確定 VM 上執行的伺服器角色支援 Sysprep。如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。執行 Sysprep 前，請先備份 VM。


## 確定 VM 的檔案格式正確

在 Azure 中，您只能使用採用 VHD 檔案格式的[第 1 代虛擬機器](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx)。VHD 的大小必須固定且為整數 MB，也就是可被 8 整除。允許的 VHD 大小上限為 1023 GB。

- 如果您的 Windows VM 映像是 VHDX 格式，請使用下列做法將它轉換成 VHD︰

	- Hyper-V︰開啟 Hyper-V，在左側選取您的本機電腦。然後在上方功能表中，按一下 [動作] > [編輯磁碟...]。按 [下一步] 並輸入這些選項來瀏覽畫面：[VHDX 檔案的路徑] > [轉換] > [VHD] > [固定大小] > [新的 VHD 檔案路徑]。按一下 [完成] 以關閉。

	- [Convert-VHD PowerShell Cmdlet](http://technet.microsoft.com/library/hh848454.aspx)︰如需詳細資訊，請閱讀部落格文章[將 Hyper-V .vhdx 轉換為 .vhd 檔案格式](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/)。

- 如果您的 Windows VM 映像是 [VMDK 檔案格式](https://en.wikipedia.org/wiki/VMDK)，使用 [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) 將它轉換為 VHD。如需詳細資訊，請閱讀部落格：[How to Convert a VMware VMDK to Hyper-V VHD (如何將 VMWare VMDK 轉換為 Hyper-V VHD)](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)。


## 準備將 VHD 上傳

本節說明如何將您的 Windows 虛擬機器一般化。Sysprep 除了會移除某些資訊之外，也會移除您的所有個人帳戶資訊。如需 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

1. 登入 Windows 虛擬機器。

2. 以系統管理員身分開啟 [命令提示字元] 視窗。切換至 **%windir%\\system32\\sysprep** 目錄，然後執行 `sysprep.exe`。

3. 在 [系統準備工具] 對話方塊中，執行下列動作：

	1. 在 [系統清理動作] 中，選取 [進入系統全新體驗 (OOBE)]，並確認已勾選 [一般化] 核取方塊。

	2. 在 [關機選項]中選取 [關機]。

	3. 按一下 [確定]。

	![啟動 Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>


## 登入 Azure

1. 開啟 Azure PowerShell，並登入您的 Azure 帳戶。

		Login-AzureRmAccount

	這會開啟一個可供您輸入 Azure 帳戶認證的快顯視窗。

2. 取得您可用訂用帳戶的訂用帳戶識別碼。

		Get-AzureRmSubscription

3. 使用訂用帳戶識別碼來設定正確的訂用帳戶。

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"

	
## 取得儲存體帳戶

您需要一個 Azure 中的儲存體帳戶來裝載上傳的 VM 映像。您可以使用現有的儲存體帳戶或建立新帳戶。

顯示可用的儲存體帳戶。

		Get-AzureRmStorageAccount

如果您想要使用現有的儲存體帳戶，請移至[上傳 VM 映像](#upload-the-vm-image-to-your-storage-account)一節。

如果您想要建立儲存體帳戶，請依照下列步驟操作：

1. 請確定您有此儲存體帳戶的資源群組。使用下列命令，找出您訂用帳戶中的所有資源群組：

		Get-AzureRmResourceGroup

2. 若要建立資源群組，請使用此命令：

		New-AzureRmResourceGroup -Name <resourceGroupName> -Location <location>

3. 使用 [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) Cmdlet，在此資源群組中建立儲存體帳戶：

		New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Location "<location>" -SkuName "<skuName>" -Kind "Storage"
			
-SkuName 的有效值包括：

- **Standard\_LRS** - 本地備援儲存體。
- **Standard\_ZRS** - 區域備援儲存體。
- **Standard\_GRS** - 異地備援儲存體。
- **Standard\_RAGRS** - 讀取權限異地備援儲存體。
- **Premium\_LRS** - 進階本地備援儲存體。



## 將 VM 映像上傳至儲存體帳戶

使用 [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) Cmdlet，將映像上傳到儲存體帳戶中的容器：

		$rgName = "<resourceGroupName>"
		$urlOfUploadedImageVhd = "<storageAccount>/<blobContainer>/<targetVHDName>.vhd"
		Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath <localPathOfVHDFile>

其中：

- **storageAccount** 是映像的儲存體帳戶名稱。

- **blobContainer** 是您要用來儲存映像的 blob 容器。如果找不到具有此名稱的現有 blob 容器，則會為您建立此容器。

- **targetVHDName** 是您想要用於所上傳 VHD 檔案的名稱。

- **localPathOfVHDFile** 是您本機電腦上 .vhd 檔案的完整路徑和名稱。


如果成功，您會得到看起來如以下的回應：

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

視您的網路連線和 VHD 檔案大小而定，此命令可能需要一些時間才能完成。




## 建立虛擬網路

建立[虛擬網路](../virtual-network/virtual-networks-overview.md)的 vNet 和 subNet。

1. 使用您自己的資訊來取代變數的值。以 CIDR 格式提供子網路的位址首碼。建立變數和子網路。

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. 使用虛擬網路的名稱來取代 **$vnetName** 的值。以 CIDR 格式提供虛擬網路的位址首碼。建立變數和具有子網路的虛擬網路。

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## 建立公用 IP 位址和網路介面

若要能夠與虛擬網路中的虛擬機器進行通訊，您需要[公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)和網路介面。

1. 使用公用 IP 位址的名稱來取代 **$ipName** 的值。建立變數和公用 IP 位址。

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
        
2. 使用網路介面的名稱來取代 **$nicName** 的值。建立變數和網路介面。

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

		

## 建立 VM

下列 PowerShell 指令碼示範如何設定虛擬機器組態，以及使用已上傳的 VM 映像做為新安裝的來源。

>[AZURE.NOTE] VM 必須位於與已上傳的 VHD 檔案相同的儲存體帳戶中。

</br>

	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account where the VHD file is and where the OS disk will be created
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"

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
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



完成時，在 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 底下，或是使用下列 PowerShell 命令，應該就可以看到新建立的 VM：

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 後續步驟

若要使用 Azure PowerShell 來管理新的虛擬機器，請參閱[使用 Azure Resource Manager 和 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)。

<!---HONumber=AcomDC_0831_2016-->