<properties
	pageTitle="建立及上傳 Linux VHD | Microsoft Azure"
	description="以包含 Linux 作業系統的傳統部署模型建立並上傳 Azure 虛擬硬碟 (VHD)。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# 建立及上傳包含 Linux 作業系統的虛擬硬碟

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 您也可以[使用 Azure Resource Manager 上傳自訂磁碟映像](virtual-machines-linux-upload-vhd.md)。

本文說明如何建立及上傳虛擬硬碟 (VHD)，以便用它做為您自己的映像，在 Azure 中建立虛擬機器。了解如何準備作業系統，以便使用它根據該映像建立多部虛擬機器。


## 必要條件
本文假設您具有下列項目：

- **以 .vhd 檔案安裝的 Linux 作業系統** - 您已經以 VHD 格式將 [Azure 背書的 Linux 散發套件](virtual-machines-linux-endorsed-distros.md) (或參閱[非背書散發套件的資訊](virtual-machines-linux-create-upload-generic.md)) 安裝到虛擬磁碟。有多項工具可用來建立 VM 和 VHD：
	- 安裝和設定 [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 或 [KVM](http://www.linux-kvm.org/page/RunningKVM)，並小心使用 VHD 做為您的映像格式。如有需要，您可以使用 `qemu-img convert` 來[轉換映像](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)。
	- 您也可以在 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 或 [ Windows Server 2012/2012 R2 ](https://technet.microsoft.com/library/hh846766.aspx) 上使用 Hyper-V。

> [AZURE.NOTE] Azure 不支援較新的 VHDX 格式。當您建立 VM 時，請指定 VHD 做為格式。如有需要，您可以使用 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 或 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell Cmdlet 將 VHDX 磁碟轉換為 VHD。此外，Azure 不支援上傳動態 VHD，因此您必須將此類磁碟轉換成靜態 VHD 再上傳。您可以在上傳至 Azure 的期間使用[適用於 GO 的 Azure VHD 公用程式](https://github.com/Microsoft/azure-vhd-utils-for-go)之類的工具來轉換動態磁碟。

- **Azure 命令列介面** - 安裝最新的 [Azure 命令列介面](../virtual-machines-command-line-tools.md)來上傳 VHD。

<a id="prepimage"> </a>
## 步驟 1：準備要上傳的映像

Azure 支援各種 Linux 散發套件 (請參閱[背書的散發套件](virtual-machines-linux-endorsed-distros.md))。下列文章會逐步引導您了解如何準備 Azure 上支援的各種 Linux 散發套件：完成下列指南中的步驟並已有一個準備好可上傳到 Azure 的 VHD 檔案之後，請回到這裡：

- **[CentOS 型散發套件](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 和 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[其他：非背書散發套件](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] 只有使用其中一個背書散發套件搭配[經 Azure 背書之配送映像上的 Linux](virtual-machines-linux-endorsed-distros.md) 中＜支援的版本＞底下指定的組態詳細資料時，Azure 平台 SLA 才適用於執行 Linux OS 的虛擬機器。Azure 映像庫中的所有 Linux 散發套件，皆為使用必要組態的背書散發套件。

如需有關為 Azure 準備 Linux 映像的更多一般秘訣，另請參閱 **[Linux 安裝注意事項](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**。


<a id="connect"> </a>
## 步驟 2：準備與 Azure 的連接

確定您是在傳統部署模型中使用 Azure CLI (`azure config mode asm`)，然後登入您的帳戶︰

```
azure login
```


<a id="upload"> </a>
## 步驟 3：將映像上傳至 Azure

您需要一個可供上傳 VHD 檔案的儲存體帳戶。您可以選取現有的儲存體帳戶或[建立新帳戶](../storage/storage-create-storage-account.md)。

使用 Azure CLI 上傳映像，方法是使用下列命令：

```bash
azure vm image create <ImageName> `
	--blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
	--os Linux <PathToVHDFile>
```

在上述範例中︰

- **BlobStorageURL** 是您打算使用的儲存體帳戶 URL
- **YourImagesFolder** 是 Blob 儲存體內您要用來儲存映像的容器
- **VHDName** 是入口網站中用來識別虛擬硬碟的顯示標籤。
- **PathToVHDFile** 是 .vhd 檔案在您電腦上的完整路徑和名稱。

以下顯示完整的範例：

```bash
azure vm image create UbuntuLTS `
	--blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
	--os Linux /home/ahmet/UbuntuLTS.vhd
```

## 步驟 4：從映像建立 VM
您使用 `azure vm create` 以和一般 VM 相同的方式建立 VM。指定您在前一個步驟中提供給映像的名稱。在下列範例中，我們會使用在前一個步驟中提供的 **UbuntuLTS** 映像名稱：

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
	--location "West US" "DeployedUbuntu" UbuntuLTS
```

若要建立您自己的 VM，請提供您自己的使用者名稱 + 密碼、位置、DNS 名稱，以及映像名稱。

## 後續步驟

如需詳細資訊，請參閱 [Azure 傳統部署模型的 Azure CLI 參考](../virtual-machines-command-line-tools.md)。

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0907_2016-->