<properties
	pageTitle="建立及上傳 Linux VHD | Microsoft Azure"
	description="使用資源管理員部署模型建立及上傳至 Azure 虛擬硬碟 (VHD)。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="iainfou"/>

# 上傳虛擬硬碟

本文說明如何使用 Resource Manager 部署模型上傳虛擬硬碟 (VHD)。這項功能可讓您安裝和設定 Linux 散發版本以符合您的需求，然後使用該 VHD 快速建立 Azure 虛擬機器 (VM)。

## 快速命令
確定已將 [Azure CLI](../xplat-cli-install.md) 登入並設定為使用 Resource Manager 模式 (`azure config mode arm`)。

首先，建立資源群組：

```bash
azure group create TestRG --location "WestUS"
```

建立的儲存體帳戶以放置虛擬磁碟：

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name LRS
```

列出您剛才建立的儲存體帳戶的儲存體金鑰，並記下 `key1`：

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

輸出將類似於：

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

使用您剛取得的儲存體金鑰在您的儲存體帳戶內建立容器︰

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

最後，將您的 VHD 上傳至您剛才建立的容器︰

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

您現在可以[使用 Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)從已上傳的虛擬磁碟建立 VM，或藉由指定您的磁碟的 URI 透過 CLI 建立 VM，如下所示︰

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

請注意，您仍然需要 `azure vm create` 命令所需的所有其他參數，例如虛擬網路、公用 IP 位址、使用者名稱、SSH 金鑰等。您可以在[可用的 CLI 資源管理員參數](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)中閱讀更多相關資訊。

## 需求
若要完成上述步驟，您需要︰

- **以 .vhd 檔案安裝的 Linux 作業系統** - 以 VHD 格式將 [Azure 背書的 Linux 散發套件](virtual-machines-linux-endorsed-distros.md) (或參閱[非背書散發套件的資訊](virtual-machines-linux-create-upload-generic.md)) 安裝到虛擬磁碟。有多項工具可用來建立 VM 和 VHD：
	- 安裝和設定 [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 或 [KVM](http://www.linux-kvm.org/page/RunningKVM)，並小心使用 VHD 做為您的映像格式。如有需要，您可以使用 `qemu-img convert` [轉換映像](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)。
	- 您也可以在 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 或 [ Windows Server 2012/2012 R2 ](https://technet.microsoft.com/library/hh846766.aspx) 上使用 Hyper-V。

> [AZURE.NOTE] Azure 不支援較新的 VHDX 格式。當您建立 VM 時，請指定原始 VHD 格式。如有需要，可以使用 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 或 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell Cmdlet 將 VHDX 轉換為 VHD 格式。此外，Azure 不支援上傳動態 VHD，因此您必須將此類磁碟轉換成靜態 VHD 再上傳。您可以在上傳至 Azure 的期間使用[適用於 GO 的 Azure VHD 公用程式](https://github.com/Microsoft/azure-vhd-utils-for-go)之類的工具來轉換動態磁碟。


<a id="prepimage"> </a>
## 準備要上傳的映像

Azure 支援各種 Linux 散發套件 (請參閱[背書散發套件](virtual-machines-linux-endorsed-distros.md))。下列文章會逐步引導您如何準備各種 Azure 支援的 Linux 散發套件。請確定您已準備好適當的虛擬硬碟，再開始上傳︰

- **[CentOS 型散發套件](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 和 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[其他：非背書散發套件](virtual-machines-linux-create-upload-generic.md)**

如需針對 Azure 準備 Linux 映像的更多秘訣，另請參閱 **[Linux 安裝注意事項](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**。

> [AZURE.NOTE] 只有在搭配[經 Azure 背書之 Linux 散發套件](virtual-machines-linux-endorsed-distros.md)中「支援的版本」下指定的組態詳細資料來使用其中一個背書散發套件時，[Azure 平台 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 才適用於執行 Linux OS 的虛擬機器。

## 後續步驟
在您備妥並上傳您的自訂虛擬磁碟後，可以閱讀[使用 Resource Manager 和範本](../resource-group-overview.md)深入了解。建議您[將資料磁碟新增](virtual-machines-linux-add-disk.md)至您的新 VM。如果您需要存取在您的 VM 上執行的應用程式，務必[開啟連接埠和端點](virtual-machines-linux-nsg-quickstart.md)。

<!---HONumber=AcomDC_0713_2016-->