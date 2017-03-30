---
title: "Azure CLI 指令碼範例 - 掛接作業系統磁碟 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 掛接作業系統磁碟"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: cdd0a11e872d81dfbb35c7a0cbfa2e1f7234dc08
ms.lasthandoff: 03/21/2017

---

# <a name="troubleshoot-a-vms-operating-system-disk"></a>針對 VM 作業系統磁碟進行疑難排解

此指令碼會將已失敗或有問題之虛擬機器的作業系統磁碟當做資料磁碟，掛接到第二部虛擬機器。 在進行磁碟問題或復原資料的疑難排解時，這非常有用。 

您可以視需要使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的指示來安裝 Azure CLI，然後執行 `az login` 來建立與 Azure 的連線。 此外，您需要一部現有的虛擬機器。 更新指令碼範例中現有虛擬機器的名稱和資源群組。

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "快速建立 VM")]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、虛擬機器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm#show) | 傳回虛擬機器清單。 此案例使用查詢選項來傳回虛擬機器的作業系統磁碟。 接著將此值新增至 'uri' 變數名稱。 |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm#delete) | 刪除虛擬機器。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 建立虛擬機器。  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) | 將磁碟連接至虛擬機器。 |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | 傳回虛擬機器的 IP 位址。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Linux VM 文件](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 CLI 指令碼範例。

