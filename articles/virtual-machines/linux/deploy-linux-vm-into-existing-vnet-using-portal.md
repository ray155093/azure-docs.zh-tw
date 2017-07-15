---
title: "使用 Azure 入口網站將 Linux VM 部署至現有網路 | Microsoft Docs"
description: "使用入口網站將 Linux VM 部署至現有 Azure 虛擬網路。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 964c0fc41773b50a9fbe476df47460484c2ada66
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---

# 如何使用 Azure 入口網站將 Linux 虛擬機器部署至現有的 Azure 虛擬網路
<a id="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-portal" class="xliff"></a>

此文章說明如何將虛擬機器 (VM) 部署至現有虛擬網路 (VNet)。 像 VNet 和網路安全性群組之類的 Azure 資產應為鮮少部署的靜態且長久資源。 VNet 部署之後，即可在不斷重新部署時重複使用，完全不會對基礎結構造成負面影響。 將 VNet 想像成傳統的硬體網路交換器，您就不需要在每次部署時設定全新的硬體交換器。  

有了正確設定的 VNet，您就可以一次又一次地將新的伺服器部署至該 VNet，整個 VNet 生命週期內所需的變動極少 (如果有的話)。

## 建立資源群組
<a id="create-the-resource-group" class="xliff"></a>

首先，建立資源群組來組織我們在本逐步解說中建立的所有項目。 如需 Azure 資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## 建立 VNet
<a id="create-the-vnet" class="xliff"></a>

接下來，建置要將 VM 送入的 VNet。 VNet 包含一個子網路，且會在稍後步驟中與含此子網路的網路安全性群組相關聯。

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## 將 VNic 新增至子網路
<a id="add-a-vnic-to-the-subnet" class="xliff"></a>

虛擬網路卡 (VNic) 很重要，因為您可以將它們連接至不同的 VM。 此方法既可讓 VM 為暫時性，又可將 VNic 保持為靜態資源。 建立 VNic，並將它與先前步驟中建立的子網路產生關聯。

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## 建立網路安全性群組
<a id="create-the-network-security-group" class="xliff"></a>

Azure 網路安全性群組相當於網路層的防火牆。 如需有關 Azure 網路安全性群組的詳細資訊，請參閱[什麼是網路安全性群組](../../virtual-network/virtual-networks-nsg.md)。

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## 新增輸入 SSH 允許規則
<a id="add-an-inbound-ssh-allow-rule" class="xliff"></a>

由於需要從網際網路存取 VM，因此會建立一個規則來允許輸入連接埠 22 流量通過網路流向 VM 的連接埠 22。

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## 將 NSG 與子網路產生關聯
<a id="associate-the-nsg-with-the-subnet" class="xliff"></a>

建立 VNet 和子網路後，請將網路安全性群組與子網路產生關聯。 網路安全性群組可以與整個子網路或個別的 VNic 產生關聯。 有了篩選子網路層級流量的防火牆，子網路內所有的 VNic 與 VM 會受到網路安全性群組的保護。 另一個方法是讓網路安全性群組只與單一 VNic 相關聯，並僅保護一個 VM。

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## 將 VM 部署至 VNet 和 NSG
<a id="deploy-the-vm-into-the-vnet-and-nsg" class="xliff"></a>

您可以使用 Azure 入口網站，將 Linux VM 部署至現有的 Azure 資源群組、VNet、子網路和 VNic。

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

您可以使用入口網站來選擇現有的資源，來指示 Azure 將 VM 部署在現有的網路內。 VNet 和子網路部署之後，就可以在 Azure 區域內保持為靜態或永久性資源。  

## 後續步驟
<a id="next-steps" class="xliff"></a>

* [使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器](../windows/cli-deploy-templates.md)
* [直接使用 Azure CLI 命令，建立自訂的 Linux VM 環境](create-cli-complete.md)
* [使用範本在 Azure 上建立 Linux VM](create-ssh-secured-vm-from-template.md)

