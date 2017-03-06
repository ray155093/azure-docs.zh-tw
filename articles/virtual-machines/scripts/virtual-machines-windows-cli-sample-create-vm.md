---
title: "Azure CLI 指令碼範例 - 建立 Windows Server VM | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立 Windows Server VM"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 777baeb2a6a281343da8bea2de957ee9883a5ba2
ms.openlocfilehash: 4cc559956cf49d002124b6035eca874996c730f9
ms.lasthandoff: 03/01/2017

---

# <a name="create-a-virtual-machine-with-the-azure-cli"></a>使用 Azure CLI 建立虛擬機器

此指令碼會建立具有 Windows Server 2016 作業系統和相關網路資源的 Azure 虛擬機器。 成功執行此指令碼後，就能透過 RDP 存取虛擬機器。

在執行這個指令碼之前，請確定您已使用 `az login` 命令建立與 Azure 的連線。 此外，您應該將指令碼開頭的 $AdminPassword 變數變更為唯一值，並符合密碼複雜性需求。

這個範例適用於 Bash 殼層。 如需在 Windows 上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../virtual-machines-windows-cli-options.md)。

## <a name="create-vm-sample"></a>建立 VM 範例

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-vm-detailed/create-windows-vm-detailed.sh "快速建立 VM")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、虛擬機器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | 建立 Azure 虛擬網路和子網路。 |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) | 建立具有靜態 IP 位址和相關聯 DNS 名稱的公用 IP 位址。 |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) | 建立網路安全性群組 (NSG)，做為網際網路和虛擬機器之間的安全性界限。 |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) | 建立虛擬網路卡，並將它連接至虛擬網路、子網路及 NSG。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 建立虛擬機器，並將它連線到網路卡、虛擬網路、子網路及 NSG。 此命令也會指定要使用的虛擬機器映像和管理認證。  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Windows VM 文件](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 CLI 指令碼範例。

