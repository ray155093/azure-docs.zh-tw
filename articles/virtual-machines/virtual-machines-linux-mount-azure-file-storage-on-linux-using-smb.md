---
title: "使用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體 | Microsoft Docs"
description: "如何使用 Azure CLI 2.0 (預覽) 利用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: dbdebe120bd6166854f3494169d9c0d5c8f7bf69
ms.openlocfilehash: 6851faf301175ed851e53088862e6f8b50ad8b3e
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-20-preview"></a>使用 Azure CLI 2.0 (預覽) 利用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體

本文說明如何藉由 SMB 掛接，在 Linux VM 上使用「Azure 檔案儲存體」服務。 Azure 檔案儲存體可在雲端中使用標準的 SMB 通訊協定提供檔案共用。  這些需求包括：

- [一個 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 公開金鑰和私密金鑰檔案](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – 適用於傳統和資源管理部署模型的 CLI
- [Azure CLI 2.0 (預覽)](#quick-commands) - 適用於資源管理部署模型的新一代 CLI (本文章)


## <a name="quick-commands"></a>快速命令

如果您需要快速完成工作，接下來這一節將詳細說明所需的命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，[從這裡開始](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough)。

必要條件︰資源群組、VNet、具有 SSH 輸入的 NSG、子網路、Azure 儲存體帳戶、Azure 儲存體帳戶金鑰、Azure 檔案儲存體共用以及 Linux VM。 以您自己的值取代所有範例。

依下列方式建立本機掛接的目錄：

```bash
mkdir -p /mnt/mymountpoint
```

依下列方式將「Azure 檔案儲存體」SMB 共用掛接到掛接點：

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

若要在重新開機之後保留掛接，請依下列方式，在 `/etc/fstab` 中新增一行：

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說

Azure 檔案儲存體可在雲端中使用標準的 SMB 通訊協定提供檔案共用。 有了最新版本的檔案儲存體，您也可以從支援 SMB 3.0 的任何作業系統掛接檔案共用。 在 Linux 上使用 SMB 掛接可以讓簡單的備份成為 SLA 所支援的強大、永久封存儲存體位置。  

將檔案從 VM 移至裝載於 Azure 檔案儲存體的 SMB 掛接，是偵錯記錄檔的好方法，因為該相同的 SMB 共用可在本機掛接至 Mac、Linux 或 Windows 工作站。 SMB 不是即時串流 Linux 或應用程式記錄檔的最佳解決方案，因為 SMB 通訊協定不是針對如此繁重的記錄職責所建置。 專用的整合記錄層級工具，像是 Fluentd，是比透過 SMB 收集 Linux 和應用程式記錄輸出更好的選擇。

對於此詳細逐步解說，我們會建立必要的先決條件，先建立 Azure 檔案儲存體共用，然後透過 SMB 在 Linux VM 上掛接它。

首先，使用 [az group create](/cli/azure/group#create) 來建立資源群組以存放我們的檔案共用。 下列範例會在 `West US` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-azure-storage-account"></a>建立 Azure 儲存體帳戶
接著，使用 [az storage account create](/cli/azure/storage/account#create) 來建立儲存體帳戶以儲存實際檔案。 下列範例會使用 `Standard_LRS` 儲存體 SKU 來建立名為 `mystorageaccount` 的儲存體帳戶：

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location westus \
    --sku Standard_LRS
```

## <a name="show-the-storage-account-keys"></a>顯示儲存體帳戶金鑰

建立儲存體帳戶時，會成對建立 Azure 儲存體帳戶金鑰。 儲存體帳戶金鑰會成對建立，因此可以輪替金鑰，不會干擾服務。 一旦您將金鑰輪替至金鑰組中的第二個金鑰，您會建立新的金鑰組。 新的儲存體帳戶金鑰一律會成對建立，確保您永遠有至少一個未使用的儲存體金鑰準備輪替。

使用 [az storage account keys list](/cli/azure/storage/account/keys#list) 來檢視儲存體帳戶金鑰。 下列範例會列出名為 `mystorageaccount` 之儲存體帳戶的儲存體帳戶金鑰：

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount
```

若要擷取單一金鑰，請使用 `--query` 旗標。 下列範例會擷取第一個金鑰 (`[0]`)：

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query '[0].{Key:value}' --output tsv
```


## <a name="create-the-azure-file-storage-share"></a>建立 Azure 檔案儲存體共用

使用 [az storage share create](/cli/azure/storage/share#create) 來建立包含 SMB 共用的「檔案儲存體」共用。 配額永遠是以 GB 為單位。 請傳遞來自上述 **az storage account keys list** 命令的其中一個金鑰。 下列範例會建立名為 `mystorageshare`、有 `10` GB 配額的共用：

```azurecli
az storage share create --name mystorageshare \
    --quota 10 \
    --account-name mystorageaccount \
    --account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>建立掛接點目錄

需要 Linux 檔案系統上的本機目錄以掛接 SMB 共用。 任何從本機掛接目錄寫入或讀取的項目會轉送給裝載於 Azure 檔案儲存體上的 SMB 共用。 下列範例會在 `/mnt/mymountdirectory` 建立本機目錄：

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>掛接 SMB 共用
依下列方式將 SMB 共用掛接到您建立的本機目錄。 請依下列方式，提供您自己的儲存體帳戶使用者名稱和儲存體帳戶金鑰作為掛接認證：

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>透過重新開機持續 SMB 掛接

一旦您重新開機 Linux VM，掛接的 SMB 共用會在關機期間取消掛接。 若要在開機時重新掛接 SMB 共用，請在 Linux `/etc/fstab` 中新增一行。 Linux 使用 `fstab` 檔案以列出它在開機期間需要掛接哪些檔案系統。 新增 SMB 共用可確保 Azure 檔案儲存體共用是 Linux VM 的永久掛接檔案系統。 可以使用 `cloud-init` 將 Azure 檔案儲存體 SMB 共用新增至新的 VM。

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>後續步驟

- [在建立期間使用 cloud-init 自訂 Linux VM](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [在 Linux VM 中新增磁碟](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [使用 Azure CLI 將 Linux VM 上的磁碟加密](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

