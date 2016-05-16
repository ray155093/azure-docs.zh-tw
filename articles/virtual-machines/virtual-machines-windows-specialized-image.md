<properties
	pageTitle="建立 Windows VM 的複本 | Microsoft Azure"
	description="了解如何在 Resource Manager 部署模型中藉由建立「特殊化映像」，來建立執行 Windows 的 Azure 虛擬機器。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# 如何在 Resource Manager 部署模型中建立 Windows 虛擬機器的複本


本文將示範如何在 Resource Manager 部署模型中使用 Azure PowerShell 和 Azure 入口網站建立執行 Windows 的 Azure 虛擬機器 (VM) 複本。它會示範如何建立 Azure VM 的**特殊化**映像，此映像會維護使用者帳戶和其他來自原始 VM 的狀態資料。特殊化映像可用於數個案例，例如將 Windows VM 從傳統部署模型移植至 Resource Manager 部署模型，或為建立於 Resource Manager 部署模型中的 Windows VM 建立備份複本。您可以利用這個方法透過作業系統和資料磁碟進行複製，然後再設定網路資源以建立新的虛擬機器。

如果您需要建立大量的類似 Windows Vm 部署，您需要「一般化」映像；因此，請參閱[如何擷取 Windows 虛擬機器](virtual-machines-windows-capture-image.md)。



## 開始之前請檢查下列項目

本文在您開始步驟之前假設您符合下列必要條件︰

1. 您有執行 Windows 的 Azure 虛擬機器，使用傳統或 Resource Manager 部署模型所建立。您已設定作業系統、附加資料磁碟，以及進行像是安裝所需的應用程式等其他自訂。我們將使用此 VM 建立複本；如果您需要建立來源 VM 的說明，請參閱[如何建立利用 Resource Manager Windows VM](virtual-machines-windows-ps-create.md)。 

1. 您已在您的電腦上安裝 Azure PowerShell 並登入您的 Azure 訂用帳戶。如需詳細資訊，請參閱[如何安裝和設定 PowerShell](../powershell-install-configure.md)。

1. 您已下載並安裝 AzCopy 工具。如需這項工具的詳細資訊，請參閱[利用 AzCopy 命令列工具傳輸資料](../storage/storage-use-azcopy.md)。

1. 您有資源群組、儲存體帳戶以及在該資源群組中建立以複製 VHD 的 blob 容器。閱讀[建立或尋找 Azure 儲存體帳戶](virtual-machines-windows-upload-image.md#createstorage)一節以使用現有的儲存體帳戶或建立一個新帳戶。



> [AZURE.NOTE] 類似的步驟可套用至使用兩種部署模型之一建立做為來源映像的 VM。我們會注意適用的細微差異。


## 將 VHD 複製到您的 Resource Manager 儲存體帳戶


1. 先藉由執行下列兩個選項之一，來釋放來源 VM 使用的 VHD：

	- 如果您想要**複製**來源虛擬機器，然後**停止**和**解除配置**它。
	
		- 針對使用傳統部署模型所建立的 VM，您可以使用[入口網站](https://portal.azure.com)，然後按一下 [瀏覽] > [虛擬機器 (傳統)] > [您的 VM] > [停止]，或使用 PowerShell 命令 `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`。 
		
		- 針對 Resource Manager 部署模型中的 VM，您可以登入入口網站，然後按一下 [瀏覽] > [虛擬機器] > [您的 VM] > [停止]，或使用 PowerShell 命令 `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`。請注意，入口網站中的 VM 「狀態」會從**正在執行**變更為**已停止 (取消配置)**。

	
	- 或者，如果您想要**移轉**來源虛擬機器，請**刪除**該 VM 並使用剩餘的 VHD。**瀏覽**至[入口網站](https://portal.azure.com)中的虛擬機器並按一下 [刪除]。
	
1. 尋找儲存體帳戶中的存取金鑰，其中包含您的來源 VHD，以及您將在其中複製 VHD 以建立新 VM 的儲存體帳戶。我們要在其中複製 VHD 的帳戶金鑰稱為「來源金鑰」，將它複製的目標帳戶稱為「目的地金鑰」。如需存取金鑰的詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

	- 如果您的來源 VM 使用傳統部署模型建立，請按一下 [瀏覽] > [儲存體帳戶 (傳統)] > [您的儲存體帳戶] > [所有設定] > [金鑰] 並複製標示為**主要存取金鑰**的金鑰。 
	
	- 針對使用 Resource Manager 部署模型所建立的 FM，或針對您將用於新 VM 的儲存體帳戶，請按一下 [瀏覽] > [儲存體帳戶] > [您的儲存體帳戶] > [所有設定] > [存取金鑰] 並複製標示為 **key1** 的金鑰。

1. 取得存取您的來源和目的地儲存體帳戶的 RUL。在入口網站中，**瀏覽**至您的儲存體帳戶並按一下 [Blob]。然後按一下裝載來源 VHD 的容器 (例如傳統部署模型的 *vhd*) 或您想要複製 VHD 的目標容器。按一下容器的 [屬性] 並複製標示為 **URL** 的文字。我們需要來源和目的地容器的 URL。這些 URL 將類似 `https://myaccount.blob.core.windows.net/mycontainer`。

1. 在您的本機電腦上，開啟命令視窗並瀏覽至安裝 AzCopy 所在的資料夾。它會類似 *C:\\Program Files (x86) \\Microsoft SDKs\\Azure\\AzCopy*。從該處執行下列命令：</br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] 您必須使用 AzCopy 分別複製作業系統和資料磁碟，如上所述。


## 使用複製的 VHD 建立 VM

下列步驟將示範如何使用上述步驟中複製的 VHD，在新的虛擬網路中使用 Azure PowerShell 建立以 Resource Manager 為基礎的 Windows VM。VHD 和即將建立的新虛擬機器應該會位在相同的儲存體帳戶中。


首先，以類似下文的指令碼設定新 VM 的虛擬網路和 NIC。為變數 (以 **$** 符號表示) 使用適合您應用程式的值。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


現在設定 VM 組態，並使用複製的 VHD 建立新的虛擬機器，如下所示。</br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
	
	#Add data disks using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
	
資料和作業系統磁碟 URL 的外觀如下︰`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`。您可以藉由下列方法在入口網站上找到它，瀏覽至目標儲存體容器，按一下複製的作業系統或資料 VHD，然後複製 **URL** 的內容。
	
	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
	
如果此命令成功，您會看到如下的輸出︰

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


從 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器]，或是使用下列 PowerShell 命令，應可看到新建立的 VM：

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

若要登入新的虛擬機器，請**瀏覽**至[入口網站](https://portal.azure.com)中的 VM，按一下 [連線] 並開啟「遠端桌面」RDP 檔案。使用原始虛擬機器的帳戶認證來登入新的虛擬機器。


## 後續步驟

若要使用 Azure PowerShell 管理新的虛擬機器，請參閱[使用 Azure Resource Manager 與 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)。

<!---HONumber=AcomDC_0504_2016-->