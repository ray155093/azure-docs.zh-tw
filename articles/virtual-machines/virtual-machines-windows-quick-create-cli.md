---
title: "Azure 快速入門 - 建立 Windows VM CLI | Microsoft Docs"
description: "快速了解如何使用 Azure CLI 建立 Windows 虛擬機器。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 3064da2a6afee007418ef6ba5ec4247e3e045c38
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立 Windows 虛擬機器

Azure CLI 2.0 用於從命令列或在指令碼中建立和管理 Azure 資源。 本指南詳細說明如何使用 Azure CLI 來部署執行 Windows Server 2016 的虛擬機器。

開始之前，請確定已安裝 Azure CLI。 如需詳細資訊，請參閱 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="log-in-to-azure"></a>登入 Azure 

使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Create virtual machine

使用 [az vm create](/cli/azure/vm#create) 來建立 VM。 

下列範例會建立名為 `myVM` 的 VM。 此範例會以 `azureuser` 做為系統管理使用者名稱並以 ` myPassword12` 做為密碼。 將這些值更新為適合您環境的值。 建立與虛擬機器的連線時需要使用這些值。

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

建立 VM 後，Azure CLI 會顯示類似下列範例的資訊。 記下公用 IP 位址。 此位址用來存取 VM。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

使用下列命令，建立與虛擬機器的遠端桌面工作階段。 以虛擬機器的公用 IP 位址取代 IP 位址。 出現提示時，請輸入您在建立虛擬機器時所使用的認證。

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>刪除虛擬機器

若不再需要，您可以使用下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

[安裝角色和設定防火牆的教學課程](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[瀏覽 VM 部署 CLI 範例](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
