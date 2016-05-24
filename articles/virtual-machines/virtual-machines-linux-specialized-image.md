<properties
	pageTitle="建立 Linux VM 的複本 | Microsoft Azure"
	description="了解如何在 Resource Manager 部署模型中，藉由建立「特殊化映像」來建立執行 Linux 的 Azure 虛擬機器。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# 如何在 Resource Manager 部署模型中建立 Linux 虛擬機器的複本



本文將示範如何在 Resource Manager 部署模型中使用 Azure CLI 和 Azure 入口網站建立執行 Linux 的 Azure 虛擬機器 (VM) 複本。它會示範如何建立 Azure VM 的**特殊化**映像，此映像會維護使用者帳戶和其他來自原始 VM 的狀態資料。特殊化映像可用於數個案例，例如將 Linux VM 從傳統部署模型移植至 Resource Manager 部署模型，或為建立於 Resource Manager 部署模型中的 Linux VM 建立備份複本。您可以利用這個方法透過作業系統和資料磁碟進行複製，然後再設定網路資源以建立新的虛擬機器。

如果您需要建立大量的類似 Linux VM 部署，您需要「一般化」映像；因此，請參閱[如何擷取 Linux 虛擬機器](virtual-machines-linux-capture-image.md)。



## 開始之前請檢查下列項目

本文假設您已經：

1. 準備好傳統或 Resource Manager 部署模型的**執行 Linux 的 Azure 虛擬機器**，且已設定作業系統、連接資料磁碟、安裝所需的應用程式。如果您需要建立此 VM 的說明，請參閱[在 Azure 中建立 Linux VM](virtual-machines-linux-quick-create-cli.md)。 

1. 電腦上已安裝 **Azure CLI**，並已登入您的 Azure 訂用帳戶。如需詳細資訊，請參閱[如何安裝 Azure CLI](../xplat-cli-install.md)。

1. 擁有搭配**儲存體帳戶**的**資源群組**，以及在此群組中建立的 **blob 容器**以將 VHD 複製至此容器。如需詳細資訊，請閱讀[使用 Azure CLI 搭配 Azure 儲存體](../storage/storage-azure-cli.md)。



> [AZURE.NOTE] 類似的步驟可套用至使用兩種部署模型之一建立做為來源映像的 VM。我們會注意適用的細微差異。


## 將 VHD 複製到您的 Resource Manager 儲存體帳戶


1. 先藉由執行下列兩個選項之一，來釋放來源 VM 使用的 VHD：

	- 如果您想要**_複製_**來源虛擬機器，請**停止**虛擬機器並**解除配置**。在入口網站中，按一下 [瀏覽] > [虛擬機器] 或 [虛擬機器 (傳統)] > [您的 VM] > [停止]。針對在 Resource Manager 部署模型中建立的 VM，您也可以使用 Azure CLI 命令 `azure vm stop <yourResourceGroup> <yourVmName>` 加上 `azure vm deallocate <yourResourceGroup> <yourVmName>`。請注意，入口網站中的 VM「狀態」會從**執行中**變更為**已停止 (已解除配置)**。
	
	- 或者，如果您想要**移轉**來源虛擬機器，請**刪除**該 VM 並使用剩餘的 VHD。在[入口網站](https://portal.azure.com)中**瀏覽**至您的虛擬機器並按一下 [刪除]。
	
1. 尋找包含來源 VHD 之儲存體帳戶的存取金鑰。如需存取金鑰的詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

	- 如果您的來源 VM 使用傳統部署模型建立，請按一下 [瀏覽] > [儲存體帳戶 (傳統)] > [您的儲存體帳戶] > [所有設定] > [金鑰] 並複製標示為**主要存取金鑰**的金鑰。或者，在 Azure CLI 中使用 `azure config mode asm` 變更為傳統模式，然後使用 `azure storage account keys list <yourSourceStorageAccountName>`。

	- 針對使用 Resource Manager 部署模型所建立的 VM，請按一下 [瀏覽] > [儲存體帳戶] > 您的儲存體帳戶 > [所有設定] > [存取金鑰] 並複製標示為 **key1** 的金鑰。或者，在 Azure CLI 中使用 `azure config mode arm` 確認您在 Resource Manager 模式下，然後使用 `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>`。

1. 使用[適用於儲存體的 Azure CLI 命令](../storage/storage-azure-cli.md)複製 VHD 檔案，如下列步驟所述。或者，如果您偏好以 UI 方法來達到相同的結果，可以改用 [Microsoft Azure 儲存體總管](http://storageexplorer.com/)。</br>
	1. 設定目的地儲存體帳戶的連接字串。該連接字串將包含此儲存體帳戶的存取金鑰。
	
			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>
	
	2. 為來源儲存體帳戶中的 VHD 檔案建立[共用存取簽章](../storage/storage-dotnet-shared-access-signature-part-1.md)。記下以下命令的**共用存取 URL** 輸出。
	
			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>
	
	3. 使用下列命令，將 VHD 從來源儲存體複製到目的地。
	
			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>
	
	4. VHD 檔案將以非同步方式複製。您可以使用下列命令來查看進度。
	
			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>
		
</br>

>[AZURE.NOTE] 您需要使用分別複製作業系統和資料磁碟，如上所述。


## 使用複製的 VHD 建立 VM

下列步驟將示範如何使用上述步驟中複製的 VHD，在新的虛擬網路中使用 Azure CLI 建立以 Resource Manager 為基礎的 Linux VM。VHD 和即將建立的新虛擬機器應該會位在相同的儲存體帳戶中。


首先，以類似下文的指令碼設定新 VM 的虛擬網路和 NIC。請根據應用程式使用合適的變數值。

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


現在，使用以下列命令複製之 VHD建立新虛擬機器。</br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>

	
資料和作業系統磁碟 URL 的外觀如下︰`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`。您可以藉由下列方法在入口網站上找到它：瀏覽至儲存體容器，按一下複製的作業系統或資料 VHD，然後複製 **URL** 的內容。
	
	
如果此命令成功，您會看到類似以下範例的輸出︰

	$azure vm create -g "testcopyRG" -n "redhatcopy" -f "LinCopyNic" -d https://testcopystore.blob.core.windows.net/testcopyblob/RedHat201631816334.vhd -x https://testcopystore.blob.core.windows.net/testcopyblob/RedHat-data.vhd -e 10 -Y -l "West US" -y Linux -z "Standard_A1" -o "testcopystore" -R "testcopyblob"
	info:    Executing command vm create
	+ Looking up the VM "redhatcopy"
	info:    Using the VM Size "Standard_A1"
	+ Looking up the NIC "LinCopyNic"
	info:    Found an existing NIC "LinCopyNic"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourceGroups/testcopyRG/providers/Microsoft.Network/virtualNetworks/LinCopyVnet/subnets/LinCopySub" in the NIC "LinCopyNic"
	info:    This NIC IP configuration has a public ip already configured "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourcegroups/testcopyrg/providers/microsoft.network/publicipaddresses/lincopyip", any public ip parameters if provided, will be ignored.
	+ Looking up the storage account testcopystore
	info:    The storage URI 'https://testcopystore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
	+ Creating VM "redhatcopy"
	info:    vm create command OK

您應該會在 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 下方看到新建立的 VM。

使用選擇的 SSH 用戶端連接新虛擬機器，再使用原始虛擬機器的帳戶認證 (如 `ssh OldAdminUser@<IPaddressOfYourNewVM>`)。若要閱讀更多以 SSH 連接 Linux VM 的相關資訊，請參閱[如何在 Azure 上搭配使用 SSH 與 Linux](virtual-machines-linux-ssh-from-linux.md)。


## 後續步驟

若要了解如何使用 Azure CLI 來管理新虛擬機器，請參閱 [Azure Resource Manager 的 Azure CLI 命令](azure-cli-arm-commands.md)。

<!---HONumber=AcomDC_0511_2016-->