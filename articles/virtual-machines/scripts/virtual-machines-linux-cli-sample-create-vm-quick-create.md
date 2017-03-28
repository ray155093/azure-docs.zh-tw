---
title: "Azure CLI 指令碼範例 - 快速建立 Linux VM | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 快速建立 Linux VM"
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
ms.openlocfilehash: ecd854842ace5a6472ea30f8cedd2a839f4f6358
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-virtual-machine"></a>建立虛擬機器

此指令碼會建立具有 Ubuntu 作業系統和相關網路資源的 Azure 虛擬機器。 執行指令碼之後，您可以透過 SSH 存取虛擬機器。

您可以視需要使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的指示來安裝 Azure CLI，然後執行 `az login` 來建立與 Azure 的連線。

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-vm-quick/create-vm-quick.sh "快速建立 VM")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、虛擬機器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 建立虛擬機器，並將它連線到網路卡、虛擬網路、子網路及網路安全性群組。 此命令也會指定要使用的虛擬機器映像和管理認證。  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Linux VM 文件](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 CLI 指令碼範例。

