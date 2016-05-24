<properties 
   pageTitle="關於虛擬網路的安全跨單位連線 | Microsoft Azure"
   description="深入了解虛擬網路的安全跨單位連線類型，包括站對站、點對站、ExpressRoute 連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# 關於虛擬網路的安全跨單位連線

本文討論將內部部署網站連線至 Azure 虛擬網路的不同方式。本文適用於資源管理員與傳統部署模型。如果您要尋找 VPN 閘道連線圖，請參閱 [Azure VPN 閘道連線拓撲](vpn-gateway-topology.md)。

可用的連線選項有三個︰站對站、點對站和 ExpressRoute。您選擇的選項可能取決於各種不同的考量，例如：


- 您的方案需要哪種輸送量?
- 您想讓通訊透過經由安全 VPN 的公用網際網路或透過私人連線?
- 您有可供使用的公用 IP 位址嗎?
- 您打算使用 VPN 裝置嗎? 如果是，它相容嗎？
- 您只連線幾台電腦或您想要網站持續連線?
- 您想要建立的方案需要什麼類型的 VPN 閘道?

下表可以協助您為方案決定最佳的連線選項。

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## 站對站連接

站對站 VPN 可讓您在您的內部部署網站和虛擬網路之間建立安全的連線。若要建立站對站連線，位於內部部署網路上的 VPN 裝置要設定為以 Azure VPN 閘道建立安全連線。一旦建立連線，您區域網路上的資源以及您虛擬網路中的資源可以進行直接且安全的通訊。站對站連線不需要為區域網路上每個用戶端電腦的虛擬網路資源存取建立個別連線。

**使用站對站連線的時機：**

- 您想建立混合式解決方案。
- 您想要在內部部署位置與虛擬網路之間建立不需要用戶端設定的連線。
- 您想要持續不中斷的連線。 

**需求**

- 內部部署 VPN 裝置必須有連結網際網路的 IPv4 IP 位址。這不能在 NAT 後方。
- 您必須擁有相容的 VPN 裝置。請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 
- 您使用的 VPN 裝置必須與您的方案需要的閘道類型相容。請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。
- 閘道 SKU 也會影響彙總輸送量。如需詳細資訊，請參閱[閘道 SKU](vpn-gateway-about-vpngateways.md#gwsku)。 

**S2S 可用的部署模型和方法**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]


## 點對站連接

點對站 VPN 可讓您建立與虛擬網路的安全連線。在點對站設定中，連線是在您想要連線到虛擬網路的每個用戶端電腦上個別設定。點對站連線不需要 VPN 裝置。這種連線會使用您在每部用戶端電腦上安裝的 VPN 用戶端。VPN 的建立方式是從內部部署用戶端電腦手動啟動連線。

點對站和站對站設定可以同時存在，但與站對站連線不同的是，無法設定點對站連線同時與 ExpressRoute 連線到相同的虛擬網路。

**使用點對站連線的時機：**

- 您只想設定幾個用戶端連線到虛擬網路。

- 您想要從遠端位置連接至您的虛擬網路。例如，從咖啡館或會議室連線。

- 您有站對站連線，但有一些需要從遠端位置連線的用戶端。

- 您不能存取符合站對站連線最低需求的 VPN 裝置。

- 您的 VPN 裝置沒有連結網際網路的 IPv4 IP 位址。

**P2S 可用的部署模型和方法**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## ExpressRoute 連線

Azure ExpressRoute 可讓您在 Azure 資料中心和內部部署或共置環境中的基礎結構之間建立私人連線。ExpressRoute 連線不會經過公用網際網路，相較於一般網際網路連線，它提供了更可靠、更快速、更低延遲、更安全的連線。

在某些情況下，使用 ExpressRoute 連線在內部部署裝置和 Azure 之間傳輸資料，可以產生重大的成本效益。有了 ExpressRoute，您可以在 ExpressRoute 位置 (交換服務提供者設備) 與 Azure 建立連線，或從您現有的 WAN 網路 (例如，網路服務提供者提供的 MPLS VPN) 直接與 Azure 連線。

如需 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。


## 後續步驟

- 如需 VPN 閘道的詳細資訊，請參閱 [關於 VPN 閘道](vpn-gateway-about-vpngateways.md)、VPN 閘道[常見問題集](vpn-gateway-vpn-faq.md)和[規劃與設計](vpn-gateway-plan-design.md)文章。

- 如需 ExpressRoute 的詳細資訊，請參閱 ExpressRoute [技術概觀](../expressroute/expressroute-introduction.md)、[常見問題集](../expressroute/expressroute-faqs.md)和[工作流程](../expressroute/expressroute-workflows.md)。

<!---HONumber=AcomDC_0518_2016-->