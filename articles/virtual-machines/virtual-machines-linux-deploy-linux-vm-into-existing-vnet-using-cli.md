---
title: "使用 Azure CLI 2.0 (預覽) 將 Linux VM 部署至現有網路 | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0 (預覽) 將 Linux 虛擬機器部署至現有虛擬網路"
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
ms.date: 01/31/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63485f0c9e151db22f23d291e2a4425dd01fb7ee
ms.openlocfilehash: b22ac95ee11fe059d36a9416434a14814da1ee7d


---

# <a name="deploy-a-linux-vm-into-an-existing-virtual-network-using-the-azure-cli-20-preview"></a>使用 Azure CLI 2.0 (預覽) 將 Linux VM 部署至現有虛擬網路

此文章說明如何使用 Azure CLI 2.0 (預覽) 將虛擬機器 (VM) 部署至現有虛擬網路。 這些需求包括：

- [一個 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)
- [SSH 公開金鑰和私密金鑰檔案](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – 適用於傳統和資源管理部署模型的 CLI
- [Azure CLI 2.0 (預覽)](#quick-commands) - 適用於資源管理部署模型的新一代 CLI (本文章)


## <a name="quick-commands"></a>快速命令
如果您需要快速完成工作，下列章節詳細說明需要的命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，[從這裡開始](#detailed-walkthrough)。

若要建立此自訂環境，您需要安裝 [Azure CLI 2.0 (預覽)](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/#login) 登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`myVnet` 和 `myVM`。

**必要條件︰**Azure 資源群組、虛擬網路與子網路、允許 SSH 連入流量的網路安全性群組，以及虛擬網路介面卡。

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>將 VM 部署至虛擬網路基礎結構

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說

像虛擬網路和網路安全性群組之類的 Azure 資產，最好是當做很少部署的靜態且長久的資源。 虛擬網路部署之後可供新的部署重複使用，完全不會對基礎結構造成負面影響。 將虛擬網路想像成傳統的硬體網路交換器，您就不需要在每次部署時設定全新的硬體交換器。 有了正確設定的虛擬網路，我們就可以一次又一次地將新的 VM 部署至該虛擬網路，整個虛擬網路生命週期內所需的變動極少 (如果有的話)。

若要建立此自訂環境，您需要安裝 [Azure CLI 2.0 (預覽)](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/#login) 登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`myVnet` 和 `myVM`。

## <a name="create-the-resource-group"></a>建立資源群組

首先，我們將建立 Azure 資源群組來組織我們在本逐步解說中建立的所有項目。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 使用 [az group create](/cli/azure/group#create) 建立資源群組。 下列範例會在 `westus` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
az group create \
    --name myResourceGroup \
    --location westus
```

## <a name="create-the-virtual-network"></a>建立虛擬網路

我們現在開始建置可放入 VM 的 Azure 虛擬網路。 如需虛擬網路的詳細資訊，請參閱[使用 Azure CLI 建立虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 使用 [az network vnet create](/cli/azure/network/vnet#create) 建立虛擬網路。 下列範例會建立名為 `myVnet` 的虛擬網路和名為 `mySubnet` 的子網路：

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location westus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>建立網路安全性群組

Azure 網路安全性群組相當於網路層的防火牆。 如需網路安全性群組的詳細資訊，請參閱[如何使用 Azure CLI 建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 使用 [az network nsg create](/cli/azure/network/nsg#create) 建立網路安全性群組。 下列範例會建立名為 `myNetworkSecurityGroup` 的網路安全性群組：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>新增輸入 SSH 允許規則

由於需要從網際網路存取 Linux VM，因此需要有規則來允許連入連接埠 22 流量通過網路流向 Linux VM 的連接埠 22。 使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 新增網路安全性群組的連入規則。 下列範例會建立名為 `myNetworkSecurityGroupRuleSSH` 的規則：

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>將子網路連接至網路安全性群組

網路安全性群組規則可以套用至子網路或特定虛擬網路介面。 讓我們將網路安全性群組連接至子網路。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) 將您的子網路連接至網路安全性群組：

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>將虛擬網路介面卡新增至子網路

虛擬網路介面卡 (VNic) 很重要，因為您可以將它們連接至不同的 VM 以重複使用它們。 雖然 VM 可以是暫時性的，但是這樣重複使用可讓您將 VNic 保持為靜態資源。 使用 [az network nic create](/cli/azure/network/nic#create) 建立 VNic，並將它與子網路產生關聯。 下列範例會建立名為 `myNic` 的 VNic：

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>將 VM 部署至虛擬網路基礎結構

我們現在有虛擬網路、子網路與做為防火牆網路安全性群組，網路安全性群組會封鎖所有連入流量 (除了用於 SSH 的連接埠 22) 來保護我們的子網路。 現在可以將 VM 部署在這個現有的網路基礎結構內。

使用 [az vm create](/cli/azure/vm#create) 建立 VM。 如需使用旗標搭配 Azure CLI 2.0 (預覽) 來部署完整 VM 的詳細資訊，請參閱[使用 Azure CLI 建立完整的 Linux 環境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup
```

我們使用 CLI 旗標來呼叫現有的資源，以指示 Azure 將 VM 部署在現有的網路內。 重申一次，虛擬網路和子網路部署之後，就可以在 Azure 區域內保持為靜態或永久性資源。 在此範例中，我們沒有建立公用 IP 位址並將它指派給 VNic，因此無法透過網際網路公開地存取此 VM。 如需詳細資訊，請參閱[使用 Azure CLI 建立具有靜態公用 IP 的 VM](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="next-steps"></a>後續步驟
如需以各種方式在 Azure 中建立虛擬機器的詳細資訊，請參閱下列資源︰

* [使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令，建立自訂的 Linux VM 環境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用範本在 Azure 上建立 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO1-->


