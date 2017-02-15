---
title: "使用 SCP 從 Linux VM 移動檔案以及將檔案移至其中 | Microsoft Docs"
description: "使用 SCP 和 SSH 金鑰組安全地從 Linux VM 移動檔案以及將檔案移至其中。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 5ca865475b82b73e5c7e1ab21f098a87548605d9
ms.openlocfilehash: fe07ea13f202f19e399a8ede163fcba932490418


---

# <a name="moving-files-to-and-from-a-linux-vm-using-scp"></a>使用 SCP 從 Linux VM 移動檔案以及將檔案移至其中

本文說明如何使用安全複製 (SCP) 從您的工作站將檔案上移至 Azure Linux VM，或從 Azure Linux VM 下移至您的工作站。  做為範例，我們會將 Azure 組態檔上移至 Linux VM，並且下拉記錄檔目錄，皆是使用 SCP 和 SSH 金鑰。   

在本文中，有下列需求︰

- [一個 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 公開金鑰和私密金鑰檔案](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>快速命令

將檔案向上複製到 Linux VM

```bash
scp file user@host:directory/targetfile
```

從 Linux VM 向下複製檔案

```bash
scp user@host:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說

在您的工作站與 Linux VM 之間快速又安全地來回移動檔案，是管理您的 Azure 基礎結構的重要部分。  在本文中我們使用 SCP 逐步進行，這是建立在 SSH 之上的工具，並包含在預設的 Linux、Mac 和 Windows Bash 殼層。

## <a name="ssh-key-pair-authentication"></a>SSH 金鑰組驗證

SCP 會針對傳輸層使用 SSH。  藉由針對傳輸使用 SSH，SSH 會處理目的地主機上的驗證，同時在 SSH 預設提供的加密通道中移動檔案。  對於 SSH 驗證，可以使用使用者名稱和密碼，但強烈建議的安全性最佳做法是使用 SSH 公用和私密金鑰驗證。 一旦 SSH 已驗證連接，則 SCP 會開始複製檔案的程序。  使用已正確設定的 `~/.ssh/config` 和 SSH 公用與私密金鑰，不需使用使用者名稱，只要使用伺服器名稱即可建立 SCP 連接。  如果您只有一個 SSH 金鑰，SCP 會在 `~/.ssh/` 目錄中尋找它，並預設使用它來登入 VM。

如需有關設定您的 `~/.ssh/config` 與 SSH 公用和私密金鑰的詳細資訊，請依照本文，[建立 SSH 金鑰](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="scp-a-file-to-a-linux-vm"></a>SCP 檔案至 Linux VM

在第一個範例中，我們會將用來部署自動化的 Azure 認證檔案向上複製到 Linux VM。  因為這個檔案包含 Azure API 認證，其中包含機密資料，所以安全性很重要，且 SSH 提供的加密通道會保護檔案的內容。

```bash
scp ~/.azure/credentials myserver:/home/ahmet/.azure/credentials
```

## <a name="scp-a-directory-from-a-linux-vm"></a>從 Linux VM SCP 目錄

對於這個範例，我們會從 Linux VM 將目錄完整的記錄檔向下複製到您的工作站。  記錄檔可能包含敏感或機密資料，並使用 SCP 確保記錄檔的內容已加密。  使用 SCP 來安全地傳輸檔案是最簡單的方式，將記錄檔目錄和檔案向下移至您的工作站同時也很安全。

```bash
scp -r myserver:/home/ahmet/logs/ /tmp/.
```

`-r` CLI 旗標指示 SCP 以遞迴方式從命令中列出的目錄點複製檔案和目錄。  另請注意，命令列語法類似 `cp` 複製命令。

## <a name="next-steps"></a>後續步驟

* [管理使用者、SSH，並使用 VMAccess 擴充功能檢查或修復 Azure Linux VM 上的磁碟](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [藉由設定 SSHD 停用 Linux VM 上的 SSH 密碼](virtual-machines-linux-mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO3-->


