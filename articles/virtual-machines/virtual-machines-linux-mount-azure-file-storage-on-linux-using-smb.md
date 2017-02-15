---
title: "使用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體 | Microsoft Docs"
description: "如何使用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體。"
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
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 6a3c4b85642a7b6eb4dbd0efc3b046a89328b3f0
ms.openlocfilehash: 711d217841690ee78321a1ae1a56571c49ef74cc


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>使用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體

這篇文章說明如何使用 SMB 掛接使用 Linux VM 上的 Azure 檔案儲存體服務。  Azure 檔案儲存體可在雲端中使用標準的 SMB 通訊協定提供檔案共用。  這些需求包括：

- [一個 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 公開金鑰和私密金鑰檔案](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="quick-commands"></a>快速命令

如果您需要快速完成工作，下列章節詳細說明需要的命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，[從這裡開始](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough)。

必要條件︰資源群組、VNet、具有 SSH 輸入的 NSG、子網路、Azure 儲存體帳戶、Azure 儲存體帳戶金鑰、Azure 檔案儲存體共用以及 Linux VM。 將任何範例換成您自己的設定。

建立本機掛接的目錄

```bash
mkdir -p /mnt/mymountpoint
```

掛接 Azure 檔案儲存體 SMB 共用至掛接點

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

若要在重新開機之後保留掛接，請新增一行至 `/etc/fstab`

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說

Azure 檔案儲存體可在雲端中使用標準的 SMB 通訊協定提供檔案共用。  有了最新版本的檔案儲存體，您也可以從支援 SMB 3.0 的任何作業系統掛接檔案共用。  在 Linux 上使用 SMB 掛接可以讓簡單的備份成為 SLA 所支援的強大、永久封存儲存體位置。  

將檔案從 VM 移至裝載於 Azure 檔案儲存體的 SMB 掛接，是偵錯記錄檔的好方法，因為該相同的 SMB 共用可在本機掛接至 Mac、Linux 或 Windows 工作站。  SMB 不是即時串流 Linux 或應用程式記錄檔的最佳解決方案，因為 SMB 通訊協定不是針對如此繁重的記錄職責所建置。  專用的整合記錄層級工具，像是 Fluentd，是比透過 SMB 收集 Linux 和應用程式記錄輸出更好的選擇。

對於此詳細逐步解說，我們會建立必要的先決條件，先建立 Azure 檔案儲存體共用，然後透過 SMB 在 Linux VM 上掛接它。

## <a name="create-the-azure-storage-account"></a>建立 Azure 儲存體帳戶

```azurecli
azure storage account create myStorageAccount \
--sku-name lrs \
--kind storage \
-l westus \
-g myResourceGroup
```

## <a name="show-the-storage-account-keys"></a>顯示儲存體帳戶金鑰

建立儲存體帳戶時，會成對建立 Azure 儲存體帳戶金鑰。  儲存體帳戶金鑰會成對建立，因此可以輪替金鑰，不會干擾服務。  一旦您將金鑰輪替至金鑰組中的第二個金鑰，您會建立新的金鑰組。  新的儲存體帳戶金鑰一律會成對建立，確保您永遠有至少一個未使用的儲存體金鑰準備輪替。

```azurecli
azure storage account keys list myStorageAccount \
--resource-group myResourceGroup
```

## <a name="create-the-azure-file-storage-share"></a>建立 Azure 檔案儲存體共用

建立檔案儲存體共用，其中包含 SMB 共用。  配額永遠是以 GB 為單位。

```azurecli
azure storage share create mystorageshare \
--quota 10 \
--account-name myStorageAccount \
--account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>建立掛接點目錄

需要 Linux 檔案系統上的本機目錄以掛接 SMB 共用。  任何從本機掛接目錄寫入或讀取的項目會轉送給裝載於 Azure 檔案儲存體上的 SMB 共用。

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>掛接 SMB 共用

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>透過重新開機持續 SMB 掛接

一旦您重新開機 Linux VM，掛接的 SMB 共用會在關機期間取消掛接。  若要在開機時重新掛接 SMB 共用，您必須新增一行至 Linux `/etc/fstab`。  Linux 使用 `fstab` 檔案以列出它在開機期間需要掛接哪些檔案系統。  新增 SMB 共用可確保 Azure 檔案儲存體共用是 Linux VM 的永久掛接檔案系統。  可以使用 `cloud-init` 將 Azure 檔案儲存體 SMB 共用新增至新的 VM。

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>後續步驟

- [在建立期間使用 cloud-init 自訂 Linux VM](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [在 Linux VM 中新增磁碟](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [使用 Azure CLI 將 Linux VM 上的磁碟加密](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO3-->


