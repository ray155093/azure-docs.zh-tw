---
title: "使用 Azure CLI 1.0 將 Linux VM 部署到現有網路 | Microsoft Docs"
description: "如何使用 Azure CLI 1.0 將 Linux VM 部署到現有的虛擬網路"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 767a3f7cadba6b1e71e5a8f5995a9db090e419dd
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---

# 如何使用 Azure CLI 1.0 將 Linux 虛擬機器部署至現有的 Azure 虛擬網路
<a id="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli-10" class="xliff"></a>

此文章說明如何使用 Azure CLI 1.0 將虛擬機器 (VM) 部署至現有虛擬網路 (VNet)。 這些需求包括：

- [一個 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)
- [SSH 公開金鑰和私密金鑰檔案](mac-create-ssh-keys.md)


## 用以完成工作的 CLI 版本
<a id="cli-versions-to-complete-the-task" class="xliff"></a>
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#quick-commands) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](deploy-linux-vm-into-existing-vnet-using-cli.md) - 適用於資源管理部署模型的新一代 CLI


## 快速命令
<a id="quick-commands" class="xliff"></a>

如果您需要快速完成工作，接下來這一節將詳細說明所需的命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，[從這裡開始](deploy-linux-vm-into-existing-vnet-using-cli.md#detailed-walkthrough)。

必要條件︰資源群組、VNet、具有 SSH 輸入的 NSG、子網路。 將任何範例換成您自己的設定。

### 將 VM 部署至虛擬網路基礎結構
<a id="deploy-the-vm-into-the-virtual-network-infrastructure" class="xliff"></a>

```azurecli
azure vm create myVM \
    -g myResourceGroup \
    -l eastus \
    -y linux \
    -Q Debian \
    -o mystorageaccount \
    -u myAdminUser \
    -M ~/.ssh/id_rsa.pub \
    -n myVM \
    -F myVNet \
    -j mySubnet \
    -N myVNic
```

## 詳細的逐步解說
<a id="detailed-walkthrough" class="xliff"></a>

像 VNet 和網路安全性群組之類的 Azure 資產應為鮮少部署的靜態且長久資源。 VNet 部署之後可供新的部署重複使用，完全不會對基礎結構造成負面影響。 將 VNet 想成是傳統硬體網路交換器。 您不需要隨著每個部署設定新的硬體交換器。 有了正確設定的 VNet，您就可以一次又一次地將新的伺服器部署至該 VNet，整個 VNet 生命週期內所需的變動極少 (如果有的話)。

## 建立資源群組
<a id="create-the-resource-group" class="xliff"></a>

首先，建立資源群組來組織我們在本逐步解說中建立的所有項目。 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)

```azurecli
azure group create myResourceGroup --location eastus
```

## 建立 VNet
<a id="create-the-vnet" class="xliff"></a>

第一個步驟是建立可放入 VM 的 VNet。 在本逐步解說中，VNet 包含的一個子網路。 如需 Azure VNet 的詳細資訊，請參閱[使用 Azure CLI 建立虛擬網路](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)

```azurecli
azure network vnet create myVNet \
    --resource-group myResourceGroup \
    --address-prefixes 10.10.0.0/24 \
    --location eastus
```

## 建立網路安全性群組
<a id="create-the-network-security-group" class="xliff"></a>

子網路是建置在現有網路安全性群組的背後，所以會在子網路之前先建置網路安全性群組。 Azure 網路安全性群組相當於網路層的防火牆。 如需 Azure 網路安全性群組的詳細資訊，請參閱[如何使用 Azure CLI 建立網路安全性群組](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

```azurecli
azure network nsg create myNetworkSecurityGroup \
    --resource-group myResourceGroup \
    --location eastus
```

## 新增輸入 SSH 允許規則
<a id="add-an-inbound-ssh-allow-rule" class="xliff"></a>

由於需要從網際網路存取 VM，因此需要有規則來允許輸入連接埠 22 流量通過網路流向 VM 的連接埠 22。

```azurecli
azure network nsg rule create inboundSSH \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range 22 \
    --destination-address-prefix 10.10.0.0/24 \
    --destination-port-range 22
```

## 將子網路新增至 VNet
<a id="add-a-subnet-to-the-vnet" class="xliff"></a>

VNet 內的 VM 必須位於子網路。 每一個 VNet 可以有多個子網路。 建立子網路並與網路安全性群組產生關聯。

```azurecli
azure network vnet subnet create mySubNet \
    --resource-group myResourceGroup \
    --vnet-name myVNet \
    --address-prefix 10.10.0.0/26 \
    --network-security-group-name myNetworkSecurityGroup
```

子網路現在已新增至 VNet 內，並與網路安全性群組及規則產生關聯。


## 將 VNic 新增至子網路
<a id="add-a-vnic-to-the-subnet" class="xliff"></a>

虛擬網路卡 (VNic) 很重要，因為您可以將它們連接至不同的 VM 以重複使用它們。 此方法既可讓 VM 為暫時性，又可將 VNic 保持為靜態資源。 建立 VNic，並將它與先前步驟中建立的子網路產生關聯。

```azurecli
azure network nic create myVNic \
    --resource-group myResourceGroup \
    --location eastus \
    ---subnet-vnet-name myVNet \
    --subnet-name mySubNet
```

## 將 VM 部署至 VNet 和 NSG
<a id="deploy-the-vm-into-the-vnet-and-nsg" class="xliff"></a>

現在您透過封鎖 SSH 的連接埠 22 以外所有的輸入流量，讓 VNet、該 VNet 內的子網路，以及網路安全性群組來保護子網路。 現在可以將 VM 部署在這個現有的網路基礎結構內。

您可以使用 Azure CLI 和 `azure vm create` 命令，將 Linux VM 部署至現有的 Azure 資源群組、VNet、子網路和 VNic。 如需使用 CLI 來部署完整 VM 的詳細資訊，請參閱[使用 Azure CLI 建立完整的 Linux 環境](create-cli-complete.md)

```azurecli
azure vm create myVM \
    --resource-group myResourceGroup \
    --location eastus \
    --os-type linux \
    --image-urn Debian \
    --storage-account-name mystorageaccount \
    --admin-username myAdminUser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --vnet-name myVNet \
    --vnet-subnet-name mySubnet \
    --nic-name myVNic
```

您可以使用 CLI 旗標來呼叫現有的資源，以指示 Azure 將 VM 部署在現有的網路內。 VNet 和子網路部署之後，就可以在 Azure 區域內保持為靜態或永久性資源。  

## 後續步驟
<a id="next-steps" class="xliff"></a>

* [使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器](../windows/cli-deploy-templates.md)
* [直接使用 Azure CLI 命令，建立自訂的 Linux VM 環境](create-cli-complete.md)
* [使用範本在 Azure 上建立 Linux VM](create-ssh-secured-vm-from-template.md)

