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
	ms.date="06/14/2016"
	ms.author="iainfou"/>

# 建立及上傳包含 Linux 作業系統的虛擬硬碟

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。

本文說明如何建立及上傳虛擬硬碟 (VHD)，以便用它做為您自己的映像，在 Azure 中建立虛擬機器。您將了解如何準備作業系統，以便用它根據該映像建立多台虛擬機器。

**重要**：只有在搭配 [經 Azure 背書之配送映像上的 Linux](virtual-machines-linux-endorsed-distros.md)的「支援的版本」下指定的組態詳細資料來使用其中一個背書散發套件時，Azure 平台 SLA 才適用於執行 Linux OS 的虛擬機器。Azure 映像庫中的所有 Linux 散發套件，皆為使用必要組態的背書散發套件。


## 必要條件
本文假設您具有下列項目：

- **以 .vhd 檔案安裝的 Linux 作業系統** - 您已經以 VHD 格式將 [Azure 背書的 Linux 散發套件](virtual-machines-linux-endorsed-distros.md) (或參閱[非背書散發套件的資訊](virtual-machines-linux-create-upload-generic.md)) 安裝到虛擬磁碟。有多項工具可用來建立 .vhd 檔案 - 例如，您可以使用虛擬化解決方案 (例如 Hyper-V) 來建立 .vhd 檔案並安裝作業系統。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

	> [AZURE.NOTE] Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 `Convert-VHD` Cmdlet，將 VHDX 轉換為 VHD 格式。此外，Azure 不支援上傳動態 VHD，因此您必須將此類磁碟轉換成靜態 VHD 再上傳。您可以使用 [Azure VHD Utilities for GO (適用於 GO 的 Azure VHD 公用程式)](https://github.com/Microsoft/azure-vhd-utils-for-go) 之類的工具來轉換動態磁碟。

- **Azure 命令列介面** - 安裝最新的 [Azure 命令列介面](../virtual-machines-command-line-tools.md)來上傳 VHD。

<a id="prepimage"> </a>
## 步驟 1：準備要上傳的映像

Azure 支援各種 Linux 散發套件 (請參閱[背書散發套件](virtual-machines-linux-endorsed-distros.md))。下列文章會逐步引導您如何準備各種 Azure 支援的 Linux 散發套件。完成以下指南中的步驟後回到這裡，您應會有一個 VHD 檔案，準備好上傳至 Azure：

- **[CentOS 型散發套件](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 和 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[其他：非背書散發套件](virtual-machines-linux-create-upload-generic.md)**

如需針對 Azure 準備 Linux 映像的詳細一般秘訣，另請參閱 **[Linux 安裝注意事項](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**。


<a id="connect"> </a>
## 步驟 2：準備與 Azure 的連接

確定您在傳統部署模型中使用 Azure CLI (`azure config mode asm`)，然後登入您的帳戶︰

```
azure login
```


<a id="upload"> </a>
## 步驟 3：將映像上傳至 Azure

您需要一個可供上傳 VHD 檔案的儲存體帳戶。您可以選取現有的或[建立新的儲存體帳戶](../storage/storage-create-storage-account.md)。

使用 Azure CLI 上傳映像，方法是使用下列命令：

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

在上述範例中︰

- **BlobStorageURL** 是您打算使用的儲存體帳戶 URL
- **YourImagesFolder** 是 Blob 儲存體中您要用來儲存映像的容器
- **VHDName** 是入口網站中，用來識別虛擬硬碟的顯示標籤。
- **PathToVHDFile** 是 .vhd 檔案在您電腦上的完整路徑和名稱。

如需詳細資訊，請參閱 [Azure 服務管理的 Azure CLI 參考](../virtual-machines-command-line-tools.md)。

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0622_2016-->