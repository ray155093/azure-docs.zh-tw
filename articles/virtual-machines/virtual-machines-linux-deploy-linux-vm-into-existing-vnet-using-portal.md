---
title: "將 Linux VM 部署至現有網路 - Azure 入口網站 | Microsoft Docs"
description: "使用入口網站將 Linux VM 部署至現有 Azure 虛擬網路。"
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
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: e64449991bc28427d8f559ed13c3bdf9160488db
ms.openlocfilehash: 8e75aa3b38df512dc93031d5a0e9047febe7ab74


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>使用入口網站將 Linux VM 部署至現有 VNet 和 NSG

本文說明如何將 VM 部署至現有的虛擬網路 (VNet)。  像 VNet 和 NSG (網路安全性群組) 之類的 Azure 資產，最好是當做很少部署的靜態且長久的資源。  VNet 部署之後，即可在不斷重新部署時重複使用，完全不會對基礎結構造成負面影響。  將 VNet 想像成傳統的硬體網路交換器，您就不需要在每次部署時設定全新的硬體交換器。  

有了正確設定的 VNet，我們就可以一次又一次地將新的伺服器部署至該 VNet，整個 VNet 生命週期內所需的變動極少 (如果有的話)。

## <a name="create-the-resource-group"></a>建立資源群組

首先，我們將部署資源群組來組織我們在本逐步解說中建立的所有項目。  如需 Azure 資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

![createResourceGroup](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>建立 VNet

第一個步驟是建立可放入 VM 的 VNet。  VNet 包含一個子網路，在稍後一個步驟中，我們會將 NSG 與此子網路產生關聯。

![createVNet](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>將 VNic 新增至子網路

虛擬網路卡 (VNics) 很重要，因為您可以將它們連接至不同的 VM，這樣可讓 VNic 保持為靜態資源，而 VM 可以是暫時的。 建立 VNic，並將它與先前步驟中建立的子網路產生關聯。

![createVNic](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>建立 NSG

Azure NSG 相當於網路層的防火牆。 如需 Azure NSG 的詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

![createNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>新增輸入 SSH 允許規則

由於需要從網際網路存取 Linux VM，因此建立規則來允許輸入連接埠 22 流量通過網路流向 Linux VM 的連接埠 22。

![createInboundSSH](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>將 NSG 與子網路產生關聯

建立 VNet 和子網路後，我們將 NSG 與子網路產生關聯。  NSG 可以與整個子網路或個別的 VNic 產生關聯。  有防火牆在子網路層級上篩選流量，子網路內的所有 VNics 和 VM 都會受到 NSG 保護。相反地，只與單一 VNic 相關聯的 NSG 只能保護一個 VM。

![associateNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>將 VM 部署至 VNet 和 NSG

您可以使用 Azure 入口網站，將 Linux VM 部署至現有的 Azure 資源群組、VNet、子網路和 VNic。

![createVM](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

我們使用入口網站來選擇現有的資源，以指示 Azure 將 VM 部署在現有的網路內。  重申一次，VNet 和子網路部署之後，就可以在 Azure 區域內保持為靜態或永久性資源。  

## <a name="next-steps"></a>後續步驟

* [使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [直接使用 Azure CLI 命令，建立自訂的 Linux VM 環境](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用範本在 Azure 上建立 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Jan17_HO4-->


