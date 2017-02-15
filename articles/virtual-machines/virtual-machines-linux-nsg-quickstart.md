---
title: "在 Azure 中針對 Linux VM 開啟連接埠和端點 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 部署模型和 Azure CLI 2.0 (預覽) 對 Linux VM 開啟連接埠 / 建立端點"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/8/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: e4512dd4d818b1c7bea7e858a397728ce48a5362
ms.openlocfilehash: 40f399c339e31d9d008230449d7f559ae01afba3


---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>在 Azure 中針對 Linux VM 開啟連接埠和端點
您可以透過在子網路或 VM 網路介面上建立網路篩選，對 Azure 中的虛擬機器 (VM) 開啟連接埠或建立端點。 您可將控制輸入和輸出流量的這些篩選器放在可接收流量的資源所附加的網路安全性群組上。 讓我們使用連接埠 80 上的 Web 流量的常見範例。 這篇文章說明如何使用 Azure CLI 2.0 (預覽) 來開啟連接埠至 VM。


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](virtual-machines-linux-nsg-quickstart-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – 適用於傳統和資源管理部署模型的 CLI
- [Azure CLI 2.0 (預覽)](#quick-commands) - 適用於資源管理部署模型的新一代 CLI (本文章)


## <a name="quick-commands"></a>快速命令
若要建立網路安全性群組和規則，您需要安裝 [Azure CLI 2.0 (預覽)](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/#login) 登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`myNetworkSecurityGroup` 和 `myVnet`。

使用 [az network nsg create](/cli/azure/network/nsg#create) 建立網路安全性群組。 下列範例會在 `westus` 位置建立名為 `myNetworkSecurityGroup` 的網路安全性群組：

```azurecli
az network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create)新增規則以允許流向您 Web 伺服器的 HTTP 流量 (或針對自己的案例 (例如 SSH 存取或資料庫連接) 進行調整)。 下列範例會建立名為 `myNetworkSecurityGroupRule` 的規則以允許連接埠 80 上的 TCP 流量︰

```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 80 --access allow
```

使用 [az network nic update](/cli/azure/network/nic#update)將「網路安全性群組」與 VM 的網路介面 (NIC) 建立關聯。 下列範例將名為 `myNic` 的現有 NIC 與名為 `myNetworkSecurityGroup` 的網路安全性群組建立關聯：

```azurecli
az network nic update --resource-group myResourceGroup --name myNic \
    --network-security-group myNetworkSecurityGroup
```

或者，您也可以使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update)將「網路安全性群組」與虛擬網路的子網路建立關聯，而不是只與單一 VM 上的網路介面建立關聯。 下列範例將 `myVnet`虛擬網路中名為 `mySubnet` 的現有子網路與名為 `myNetworkSecurityGroup` 的網路安全性群組建立關聯：

```azurecli
az network vnet subnet update --resource-group myResourceGroup \
    --vnet-name myVnet --name mySubnet --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>網路安全性群組的詳細資訊
這裡的快速命令可讓您使流向您 VM 的流量開始正常運作。 「網路安全性群組」提供許多絕佳的功能和細微性來控制對您資源的存取。 您可以深入了解 [建立網路安全性群組和 ACL 規則](../virtual-network/virtual-networks-create-nsg-arm-cli.md)。

您可以在 Azure Resource Manager 範本中定義網路安全性群組和 ACL 規則。 深入了解 [使用範本建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您需要使用連接埠轉送來將唯一的外部連接埠對應至您 VM 上的內部連接埠，請使用負載平衡器和「網路位址轉譯」(NAT) 規則。 例如，您可能會想要對外公開 TCP 連接埠 8080，然後讓流量導向到 VM 上的 TCP 連接埠 80。 您可以深入了解 [建立網際網路面向的負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-cli.md)。

## <a name="next-steps"></a>後續步驟
在此範例中，您建立了簡單的規則來允許 HTTP 流量。 您可以從下列文章中，找到有關建立更詳細環境的資訊︰

* [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)
* [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
* [負載平衡器的 Azure Resource Manager 概觀](../load-balancer/load-balancer-arm.md)




<!--HONumber=Dec16_HO2-->


