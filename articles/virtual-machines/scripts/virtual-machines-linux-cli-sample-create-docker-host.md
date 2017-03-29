---
title: "Azure CLI 指令碼範例 - 建立 Docker 主機 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立 Docker 主機"
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
ms.date: 03/15/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a82b96bd0a669d9798467c837bf34133b3f9cd0c
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-vm-with-docker"></a>建立含 Docker 功能的 VM

這個指令碼會建立啟用 Docker 功能的虛擬機器，並啟動執行 NGINX 的 Docker 容器。 執行這個指令碼之後，您可以透過 Azure 虛擬機器的 FQDN 存取 NGINX Web 伺服器。 

您可以視需要使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的指示來安裝 Azure CLI，然後執行 `az login` 來建立與 Azure 的連線。

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Docker 主機")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 建立虛擬機器，並將它連線到網路卡、虛擬網路、子網路及網路安全性群組。 此命令也會指定要使用的虛擬機器映像和管理認證。  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | 建立網路安全性群組規則以允許輸入流量。 在此範例中，會開放連接埠 80 供 HTTP 流量使用。 |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | 對 VM 新增並執行虛擬機器擴充功能。 在此範例中，使用 Docker VM 延伸模組來設定 Docker 主機。|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 刪除資源群組，包括所有巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Linux VM 文件](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 CLI 指令碼範例。

