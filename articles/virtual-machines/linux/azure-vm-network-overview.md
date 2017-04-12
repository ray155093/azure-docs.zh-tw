---
title: "Azure 與 Linux VM 網路概觀 |Microsoft Docs"
description: "Azure 與 Linux VM 網路概觀。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: b5420e35-0463-4456-9803-6142db150f2e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1ff4a0482d6dc6ec0eceaa89ca4b87ba1e2f89a1
ms.lasthandoff: 04/03/2017


---
# <a name="azure-and-linux-vm-network-overview"></a>Azure 與 Linux VM 網路概觀
## <a name="virtual-networks"></a>虛擬網路
Azure 虛擬網路 (VNet) 是您的網路在雲端中的身分。 它是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 您可以完全控制此網路內的 IP 位址區塊、DNS 設定、安全性原則和路由表。 您也可以進一步將 VNet 分成子網路，並啟動 Azure IaaS 虛擬機器 (VM) 和/或雲端服務 (PaaS 角色執行個體)。 另外，您也可以使用 Azure 中提供的其中一個連線選項將虛擬網路連線到內部部署網路。 基本上，您可以將您的網路延伸至 Azure，透過 Azure 提供的企業級好處完整控制 IP 位址區塊。

* [虛擬網路概觀](../../virtual-network/virtual-networks-overview.md)

若要使用 Azure CLI 建立 VNet，請前往這裡並遵循逐步解說。

* [如何透過 Azure CLI 建立 VNet](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)

## <a name="network-security-groups"></a>網路安全性群組
網路安全性群組 (NSG) 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕虛擬網路中 VM 執行個體的網路流量。 NSG 可與子網路或該子網路內的個別 VM 執行個體相關聯。 當 NSG 與子網路相關聯時，ACL 規則便會套用至該子網路中的所有 VM 執行個體。 此外，將 NSG 直接關聯至該 VM 即可進一步限制個別 VM 的流量。

* [什麼是網路安全性群組 (NSG)？](../../virtual-network/virtual-networks-nsg.md)
* [如何在 Azure CLI 中建立 NSG](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

## <a name="user-defined-routes"></a>使用者定義的路由
當您在 Azure 中將虛擬機器 (VM) 新增到虛擬網路 (VNet) 時，您會發現 VM 能自動透過網路彼此通訊。 您不需要指定閘道，即使 VM 位於不同子網路。 VM 到公開網際網路的通訊同樣適用，甚至當存在 Azure 到您的資料中心的混合連線時，也適用於您的內部網路。

* [什麼是使用者定義路由和 IP 轉送？](../../virtual-network/virtual-networks-udr-overview.md)
* [在 Azure CLI 建立 UDR](../../virtual-network/virtual-network-create-udr-arm-cli.md)

## <a name="associating-a-fqdn-to-your-linux-vm"></a>將 FQDN 與您的 Linux VM 產生關聯
在 Azure 入口網站中使用 Resource Manager 部署模型來建立虛擬機器 (VM) 時，會自動建立虛擬機器的公用 IP 資源。 您可以使用此 IP 位址從遠端存取 VM。 雖然入口網站預設不會建立完整網域名稱，或稱為 FQDN，但是您可以在 VM 建立之後新增一個。

* [在 Azure 入口網站中建立完整格式的網域名稱](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="network-interfaces"></a>網路介面
網路介面 (NIC) 是虛擬機器 (VM) 與基礎軟體網路之間互相連線的橋樑。 本文說明什麼是網路介面，以及在 Azure Resource Manager 部署模型中如何加以使用。

* [虛擬網路介面](../../virtual-network/virtual-network-network-interface.md)

## <a name="virtual-nics-and-dns-labeling"></a>虛擬 NIC 和 DNS 標籤
如果您有需要持續運作的伺服器，但是該伺服器被視為 cattle 並且頻繁地拆除和部署，您會想要在您的 NIC 上使用 DNS 標籤以在 VNET 上保持名稱。  您將使用下列逐步解說，設定持續具名的 NIC 與靜態 IP。

* [使用 Azure CLI 建立完整的 Linux 環境](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network-gateways"></a>虛擬網路閘道
虛擬網路閘道用來傳送 Azure 虛擬網路和內部部署位置之間，以及 Azure 內的虛擬網路 (VNet 對 VNet) 之間的網路流量。 當您設定 VPN 閘道時，您必須建立及設定虛擬網路閘道和虛擬網路閘道連線。

* [關於 VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="internal-load-balancing"></a>內部負載平衡
Azure 負載平衡器是第 4 層 (TCP、UDP) 負載平衡器。 此負載平衡器可藉由在負載平衡器集合中，將連入流量分散於雲端服務或虛擬機器中狀況良好的服務執行個體之間，來提供高可用性。 Azure Load Balancer 也會在多個連接埠、多個 IP 位址或兩者上顯示這些服務。

* [使用 Azure CLI 建立內部負載平衡器](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)


