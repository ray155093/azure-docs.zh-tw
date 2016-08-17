<properties
	pageTitle="建立 Azure Linux VM 的複本 | Microsoft Azure"
	description="了解如何在 Resource Manager 部署模型中建立 Azure Linux 虛擬機器的複本"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="cynthn"/>

# 建立在 Azure 上執行的 Linux 虛擬機器複本


本文示範如何使用 Resource Manager 部署模型來建立執行 Linux 的 Azure 虛擬機器 (VM) 複本。首先，您需將作業系統和資料磁碟複製到新容器中，然後設定網路資源並建立新的虛擬機器。

您也可以[上傳自訂磁碟映像並從這個映像建立 VM](virtual-machines-linux-upload-vhd.md)。


## 開始之前

請先確保符合下列必要條件再開始以下步驟︰

- 您已在電腦上下載及安裝 [Azure CLI](../xplat-cli-install.md)。

- 您也需要現有 Azure Linux VM 的一些相關資訊：

| 來源 VM 資訊 | 從哪裡取得 |
|------------|-----------------|
| VM 名稱 | `azure vm list` |
| 資源群組名稱 | `azure vm list` |
| 位置 | `azure vm list` |
| 儲存體帳戶名稱 | `azure storage account list -g <resourceGroup>` |
| 容器名稱 | `azure storage container list -a <sourcestorageaccountname>` |
| 來源 VM VHD 檔案名稱 | `azure storage blob list --container <containerName>` |



- 您將需要進行新 VM 的一些相關選擇：<br> -容器名稱 <br> -VM 名稱 <br> -VM 大小 <br> -vNet 名稱 <br> -SubNet 名稱 <br> -IP 名稱 <br> -NIC 名稱
	

## 登入及設定您的訂用帳戶

1. 登入 CLI。
		
		azure login

2. 確定您處於 Resource Manager 模式。
	
		azure config mode arm

3. 設定正確的訂用帳戶。您可以使用 'azure account list' 來查看您的所有訂用帳戶。

		azure account set <SubscriptionId>



## 停止 VM 

將來源 VM 停止並解除配置。您可以使用 'azure vm list' 來取得訂用帳戶中所有 VM 及其資源群組名稱的清單。
	
		azure vm stop <ResourceGroup> <VmName>
		azure vm deallocate <ResourceGroup> <VmName>




## 複製 VHD


您可以使用 `azure storage blob copy start` 將 VHD 從來源儲存體複製到目的地。在此範例中，我們將會把 VHD 複製到相同的儲存體帳戶但不同的容器中。

若要將 VHD 複製到相同儲存體帳戶中的另一個容器，請輸入：

		azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
		

## 為新 VM 設定虛擬網路

為新 VM 設定虛擬網路和 NIC。

	azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

	azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

	azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

	azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## 建立新 VM 

您現在可以[使用 Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)從已上傳的虛擬磁碟建立 VM，或透過 CLI 藉由輸入下列命令來指定所複製磁碟的 URI 以建立 VM︰

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## 後續步驟

若要了解如何使用 Azure CLI 來管理新虛擬機器，請參閱 [Azure Resource Manager 的 Azure CLI 命令](azure-cli-arm-commands.md)。

<!---HONumber=AcomDC_0803_2016-->