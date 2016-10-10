<properties
	pageTitle="建立 Windows VM 的複本 | Microsoft Azure"
	description="了解如何在 Resource Manager 部署模型中藉由建立「特殊化映像」，來建立執行 Windows 的 Azure 虛擬機器。"
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
	ms.date="09/27/2016"
	ms.author="cynthn"/>

# 在 Azure Resource Manager 部署模型中建立 Windows 虛擬機器的複本


本文會示範如何建立執行 Windows 的 Azure 虛擬機器 (VM) 複本。具體來說，它涵蓋了使用 Azure PowerShell 與 Azure 入口網站在 Azure Resource Manager 部署模型中執行這項操作的方法。它會示範如何建立 Azure VM 的特殊化映像，此映像會維護使用者帳戶和其他來自原始 VM 的狀態資料。特殊化映像可用於將 Windows VM 從傳統部署模型移植至 Resource Manager 部署模型，或為建立於 Resource Manager 部署模型中的 Windows VM 建立備份複本。您可以使用這個方法來透過作業系統和資料磁碟進行複製，然後再設定網路資源以建立新的虛擬機器。

如果您需要建立類似 Windows VM 的大量部署，您應該使用一般化映像。相關資訊請參閱[如何擷取 Windows 虛擬機器](virtual-machines-windows-capture-image.md)。



## 開始之前

請先確保符合下列必要條件再開始以下步驟︰

- **您有執行 Windows 的 Azure 虛擬機器**，它是使用傳統或 Resource Manager 部署模型所建立。您已設定作業系統、附加資料磁碟和進行像是安裝所需的應用程式等其他自訂。您會使用這部 VM 建立複本。如果您需要建立來源 VM 的說明，請參閱[如何使用 Resource Manager 建立 Windows VM](virtual-machines-windows-ps-create.md)。

- 您已在機器上安裝 **Azure PowerShell 1.0 (或更新版本)**，並已登入 Azure 訂用帳戶。如需詳細資訊，請參閱[如何安裝及設定 Azure PowerShell](../powershell-install-configure.md)：

- 您已下載並安裝 **AzCopy 工具**。如需這項工具的詳細資訊，請參閱[使用 AzCopy 命令列公用程式傳輸資料](../storage/storage-use-azcopy.md)。

- 您有**資源群組**、**儲存體帳戶**和在該資源群組中建立以複製 VHD 的 **Blob 容器**。如需使用現有儲存體帳戶或建立新帳戶的步驟，請參閱[建立或尋找 Azure 儲存體帳戶](virtual-machines-windows-upload-image.md#createstorage)。

> [AZURE.NOTE] 使用兩種部署模型之一作為來源映像建立的 VM，適用類似的步驟。如果可行，本文會註明細微的差異。


## 將 VHD 複製到您的 Resource Manager 儲存體帳戶


1. 先執行下列兩個選項之一，釋放來源 VM 使用的 VHD：

	- 如果您想要複製來源虛擬機器，請停止虛擬機器並解除配置。

		- 針對使用傳統部署模型所建立的 VM，您可以使用[入口網站](https://portal.azure.com)，然後按一下 [瀏覽] > [虛擬機器 (傳統)] > 您的 VM > [停止]，或使用 PowerShell 命令 `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`。

		- 針對使用 Resource Manager 部署模型所建立的 VM，您可以登入入口網站，然後按一下 [瀏覽] > [虛擬機器 (傳統)] > 您的 VM > [停止]，或使用 PowerShell 命令 `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`。請注意，入口網站中的 VM 狀態會從 [執行中] 變更為 [已停止 (已解除配置)]。

	- 如果您想要移轉來源虛擬機器，請刪除該 VM 並使用剩餘的 VHD。在[入口網站](https://portal.azure.com)中瀏覽至您的虛擬機器並按一下 [刪除]。

1. 尋找儲存體帳戶中的存取金鑰，其中包含您的來源 VHD，以及您將在其中複製 VHD 以建立新 VM 的儲存體帳戶。我們要在其中複製 VHD 的帳戶金鑰稱為「來源金鑰」，將它複製的目標帳戶稱為「目的地金鑰」。如需存取金鑰的詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

	- 如果您使用傳統部署模型建立來源 VM，請按一下 [瀏覽] > [儲存體帳戶 (傳統)] > 您的儲存體帳戶 > [所有設定] > [金鑰]。複製標示為**主要存取金鑰**的金鑰。

	- 針對使用 Resource Manager 部署模型所建立的來源 VM，或針對您將用於新 VM 的儲存體帳戶，請按一下 [瀏覽] > [儲存體帳戶] > 您的儲存體帳戶 > [所有設定] > [存取金鑰]。複製標示為 **key1** 的金錀。

1. 取得存取您的來源和目的地儲存體帳戶的 RUL。在入口網站中，瀏覽至您的儲存體帳戶並按一下 [Blob]。然後按一下裝載來源 VHD 的容器 (例如，傳統部署模型的 *vhd*) 或想要放置複製 VHD 的容器。按一下容器的 [屬性] 並複製標示為 **URL** 的文字。您會需要來源和目的地容器的 URL。這些 URL 看起來類似 `https://myaccount.blob.core.windows.net/mycontainer`。

1. 在您的本機電腦上，開啟命令視窗並瀏覽至安裝 AzCopy 所在的資料夾。(它會類似 *C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\AzCopy*。) 從該處執行下列命令：</br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] 您應該如前一步驟所述，使用 AzCopy 分別複製作業系統和資料磁碟。


## 使用複製的 VHD 建立 VM

使用前一步驟中複製的 VHD，您現在可以使用 Azure PowerShell 在新的虛擬網路中建立以 Resource Manager 為基礎的 Windows VM。VHD 和即將建立的新虛擬機器應該會位在相同的儲存體帳戶中。


以類似下文的指令碼設定新 VM 的虛擬網路和 NIC。使用適合您應用程式的值來設定變數 (以 **$** 符號表示)。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


現在設定 VM 組態，並使用複製的 VHD 建立新的虛擬機器。</br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk by using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows

	#Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach

資料和作業系統磁碟的 URL 近似於︰`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`。您可以藉由下列方法在入口網站上找到它：瀏覽至目標儲存體容器，按一下複製的作業系統或資料 VHD，然後複製 URL 的內容。

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

如果此命令成功，您會看到如下的輸出︰

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


從 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 下，或是使用下列 PowerShell 命令，應可看到新建立的 VM：

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

若要登入新的虛擬機器，請瀏覽至[入口網站](https://portal.azure.com)中的 VM，按一下 [連接] 並開啟遠端桌面 RDP 檔案。使用原始虛擬機器的帳戶認證來登入新的虛擬機器。


## 後續步驟

若要使用 Azure PowerShell 管理新的虛擬機器，請參閱[使用 Azure Resource Manager 與 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)。

<!---HONumber=AcomDC_0928_2016-->