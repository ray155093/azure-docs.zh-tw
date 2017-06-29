---
title: "Azure 虛擬網路對等互連 | Microsoft Docs"
description: "了解 Azure 中的虛擬網路對等互連。"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: narayan
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 87853ff1e33007137c65e03882b5ebc74a372023
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017


---
# <a name="virtual-network-peering"></a>虛擬網路對等互連
虛擬網路對等互連可讓您透過 Azure 骨幹網路來連線同一區域中的兩個虛擬網路。 一旦對等互連，針對用作連線而言兩個虛擬網路看起來就像一個。 這兩個虛擬網路仍作為不同的資源進行管理，但對等互連虛擬網路中的虛擬機器可以使用私人 IP 位址彼此直接通訊。

在對等互連虛擬網路中虛擬機器之間的流量會透過 Azure 基礎結構路由傳送，如同在同一虛擬網路中虛擬機器之間路由傳送的流量。 使用虛擬網路對等互連的一些優點包括︰

* 不同虛擬網路的資源之間具有低延遲、高頻寬連線。
* 可以在對等互連的虛擬網路中將網路設備和 VPN 閘道等資源當做傳輸點。
* 能夠對等互連透過 Azure Resource Manager 部署模型建立的兩個虛擬網路，或將透過 Resource Manager 建立的虛擬網路對等互連至透過傳統部署模型建立的虛擬網路。 若要深入了解兩個 Azure 部署模型之間的差異，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md)一文。

虛擬網路對等互連的需求和重要層面︰

* 對等互連的虛擬網路必須存在於同一個 Azure 區域中。
* 對等互連的虛擬網路必須有非重疊的 IP 位址空間。
* 一旦虛擬網路與另一個虛擬網路對等互連，便無法在虛擬網路中新增或刪除位址空間。
* 虛擬網路對等互連是介於兩個虛擬網路之間。 沒有衍生跨對等項目的可轉移關聯性。 例如，如果 virtualNetworkA 與 virtualNetworkB 對等互連，而 virtualNetworkB 與 virtualNetworkC 對等互連，則 virtualNetwork A「不會」對等互連至 virtualNetworkC。
* 只要兩個訂用帳戶之間有一個具有權限的使用者授權對等互連，您就可以對等互連存在於兩個不同訂用帳戶中的虛擬網路，而訂用帳戶會與相同的 Azure Active Directory 租用戶產生關聯。
* 如果兩個虛擬網路都是透過 Resource Manager 部署模型建立，或如果其中一個是透過 Resource Manager 部署模型建立，而另一個是透過傳統部署模型建立，即可對等互連 VNet。 然而，透過傳統部署模型建立的兩個虛擬網路無法彼此對等互連。 對等互連透過不同部署模型建立的虛擬網路時，虛擬網路必須同時存在於「相同」的訂用帳戶中。 能夠對等互連透過存在於「不同」訂用帳戶中的部署模型建立的虛擬網路，而該功能屬於**預覽**版本。 如需進一步的詳細資訊，請閱讀[建立虛擬網路對等互連](virtual-network-create-peering.md#different-subscriptions-different-deployment-models)一文。
* 雖然對等互連虛擬網路中虛擬機器之間的通訊沒有其他頻寬限制，但仍然有基於虛擬機器大小的網路頻寬上限。 若要深入了解不同虛擬機器大小的網路頻寬上限，請閱讀 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小文章。

![基本虛擬網路對等互連](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>連線能力
兩個虛擬網路對等互連後，虛擬網路中的虛擬機器或雲端服務角色可以直接與連線至對等互連虛擬網路的其他資源連線。 這兩個虛擬網路具有完整的 IP 層級連線能力。

在對等互連虛擬網路中，兩部虛擬機器之間的來回網路延遲與在單一虛擬網路中的來回網路延遲相同。 網路輸送量為依照虛擬機器大小，按比例允許的頻寬。 對等互連內的頻寬沒有其他額外限制。

已對等互連虛擬網路中虛擬機器之間的流量會透過 Azure 後端基礎結構直接路由傳送，而不是透過閘道。

在對等互連的虛擬網路中，已連線至虛擬網路的虛擬機器可以存取內部負載平衡端點。 如有需要，可以將網路安全性群組套用在任一個虛擬網路，以封鎖其他虛擬網路或子網路的存取權限。

設定虛擬網路對等互連時，您可以開啟或關閉虛擬網路之間的網路安全性群組規則。 如果您開啟對等互連的虛擬網路 (預設選項) 之間的完整連線，您可以將網路安全性群組套用至特定子網路或虛擬機器，以封鎖或拒絕特定的存取。 若要深入了解網路安全性群組，請閱讀[網路安全性群組概觀](virtual-networks-nsg.md)一文。

Azure 提供的虛擬機器內部 DNS 名稱解析無法在對等互連虛擬網路中運作。 虛擬機器必須具有只在本機虛擬網路內可解析的內部 DNS 名稱。 不過，您可以將連線至對等互連虛擬網路的虛擬機器設定為虛擬網路的 DNS 伺服器。 如需進一步詳細資訊，請閱讀[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)一文。

## <a name="service-chaining"></a>服務鏈結
您可以設定使用者定義的路由，指向對等互連虛擬網路中當做「下一個躍點」IP 位址的虛擬機器，以啟用服務鏈結。 服務鏈結可讓您透過使用者定義的路由，將流量從一個虛擬網路導向對等互連虛擬網路中的虛擬設備。

您也可以有效地建立中樞和輪輻類型的環境，讓中樞得以裝載網路虛擬應用裝置等基礎結構元件。 所有輪輻虛擬網路可以接著與中樞虛擬網路對等互連。 流量可以通過在中樞虛擬網路中執行的網路虛擬設備。 簡單來說，虛擬網路 對等互連可讓使用者定義路由上的下一個躍點 IP 位址成為對等互連虛擬網路中虛擬機器的 IP 位址。 若要深入了解使用者定義的路由，請閱讀[使用者定義的路由概觀](virtual-networks-udr-overview.md)一文。

## <a name="gateways-and-on-premises-connectivity"></a>閘道及內部部署連線能力
不論每個虛擬網路是否與其他虛擬網路對等互連，它們仍可以擁有自己的閘道並使用它來連線至內部部署網路。 即使虛擬網路已對等互連，您也可以使用閘道來設定[虛擬網路對虛擬網路連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

當針對虛擬網路內部連線的兩個選項已設定時，虛擬網路之間的流量將會透過對等互連設定流動 (也就是透過 Azure 骨幹)。

當虛擬網路已對等互連時，您也可以將對等互連虛擬網路中的閘道設定為內部部署網路的傳輸點。 在此情況下，使用遠端閘道的虛擬網路不能擁有專屬閘道。 虛擬網路只能擁有一個閘道。 閘道可以是本機或遠端閘道 (在對等互連的虛擬網路中)，如下圖所示：

![VNet 對等互連傳輸](./media/virtual-networks-peering-overview/figure02.png)

透過不同部署模型在虛擬網路之間建立的對等互連關聯性中不支援閘道傳輸。 對等互連關聯性中的兩個虛擬網路都必須透過 Resource Manager 建立，閘道傳輸才能運作。

當共用單一 Azure ExpressRoute 連線的虛擬網路已對等互連時，它們之間的流量會經過對等互連關聯性 (也就是透過 Azure 骨幹網路)。 您依然可以在每個虛擬網路中使用本機閘道來連線內部部署線路。 此外，您也可以使用共用閘道並設定內部部署連線的傳輸。

## <a name="provisioning"></a>佈建
虛擬網路對等互連是需要權限的作業。 它是 VirtualNetworks 命名空間下的個別功能。 使用者可以取得特定權限以授權對等互連。 具有虛擬網路讀寫權限的使用者會自動繼承這些權限。

身為系統管理員或具有對等互連功能權限的使用者可以啟動對另一個虛擬網路的對等互連作業。 如果另一端有對等互連的相符要求，而且其他需求也符合，就會建立對等互連。

## <a name="limits"></a>限制
單一虛擬網路允許的對等互連數目有其限制。 如需詳細資訊，請檢閱 [Azure 網路限制](../azure-subscription-service-limits.md#networking-limits)。

## <a name="pricing"></a>價格
我們會針對使用虛擬網路對等互連的輸入和輸出流量收取少許費用。 如需詳細資訊，請參閱 [價格頁面](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="next-steps"></a>接續步驟

* 完成[虛擬網路對等互連教學課程](virtual-network-create-peering.md)
* 了解所有[虛擬網路對等互連設定，以及如何變更它們](virtual-network-manage-peering.md)。
