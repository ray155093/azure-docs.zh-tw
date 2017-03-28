---
title: "在 Azure 中重新部署 Linux 虛擬機器 | Microsoft Docs"
description: "如何在 Azure 中重新部署 Linux 虛擬機器，以減輕 SSH 連線問題。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 76769a10177457d46a280d931b273958fecb142a
ms.lasthandoff: 03/21/2017


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>將 Linux 虛擬機器重新部署至新的 Azure 節點
如果您在 Azure 中進行對 Linux 虛擬機器 (VM) 的 SSH 或應用程式存取疑難排解時一直遇到問題，重新部署 VM 也許可以解決。 重新部署 VM 時會將 VM 移到 Azure 基礎結構內的新節點，然後重新開啟它的電源，保留所有組態選項和相關聯的資源。 本文將說明如何使用 Azure CLI 或 Azure 入口網站來重新部署 VM。

> [!NOTE]
> 重新部署 VM 之後，暫存磁碟會遺失，而系統會更新與虛擬網路介面關聯的動態 IP 位址。 

您可以使用下列其中一個選項來重新部署 VM。 您只需要選擇一個選項來重新部署 VM：

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure 入口網站](#using-azure-portal)

## <a name="azure-cli-20"></a>Azure CLI 2.0
請安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 並使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。

使用 [az vm redeploy](/cli/azure/vm#redeploy) 來重新部署 VM。 下列範例會重新部署名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM：

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="azure-cli-10"></a>Azure CLI 1.0
安裝[最新的 Azure CLI 1.0](../cli-install-nodejs.md)登入 Azure 帳戶，然後確定您處於 Resource Manager 模式 (`azure config mode arm`)。

下列範例會重新部署名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM：

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>後續步驟
如果您在連接至 VM 時發生問題，您可以在[針對 SSH 連線進行疑難排解](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[詳細的 SSH 疑難排解步驟](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到具體的說明。 如果無法存取在您 VM 上執行的應用程式，您也可以參閱[應用程式疑難排解問題](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


