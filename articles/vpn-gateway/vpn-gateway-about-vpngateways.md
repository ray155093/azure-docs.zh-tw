<properties 
   pageTitle="重設 VPN 閘道 | Microsoft Azure"
   description="了解 Azure 虛擬網路的 VPN 閘道連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# 關於 VPN 閘道


VPN 閘道是用來在虛擬網路和內部部署位置之間傳送網路流量之設定的集合。VPN 閘道可用於網站間、點對站和 ExpressRoute 連線。VPN 閘道也可用來傳送 Azure 內多個虛擬網路之間的流量 (VNet 對 VNet)。

每個虛擬網路只能有一個虛擬網路閘道。若要建立連線，您可將虛擬網路閘道新增至 VNet，並進行額外的 VPN 閘道設定。在某些情況下，您建立的連線是 VPN 連線。在其他情況下，您的組態不需要 VPN。不論您的連線是否需要 VPN，設定集合都稱為「 VPN 閘道」。

當您設定 VPN 閘道時，您使用的指示取決於用來建立虛擬網路的部署模型。例如，如果您使用傳統部署模型建立 VNet，您會使用傳統部署模型的指導方針和指示來建立和進行 VPN 閘道設定。如需詳細資訊，請參閱[了解 Resource Manager 和傳統部署模型](../resource-manager-deployment-model.md)。

下列各節包含的資料表會列出下列組態資訊︰

- 可用的部署模型
- 可用的設定工具
- 直接帶您前往某篇文章的連結 (如果可用)


使用圖形和描述來協助選取符合您需求的組態拓撲。這些圖表顯示主要基準拓撲，但您也可以使用這些圖表做為指導方針來建置更複雜的組態。每個組態都會依賴您所選取的 VPN 閘道設定。

因為 VPN 閘道是設定集合，您可以使用一個工具設定部份設定，然後切換到另一個工具。目前，您無法在 Azure 入口網站中設定每一項 VPN 閘道設定。文章中各組態的指示會指定是否需要特定工具。如果您使用傳統部署模型，您此時可以在傳統入口網站中作業或使用 PowerShell。如需可用的個別設定的詳細資訊，請參閱[關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md)。


## 網站間和多站台

### 網站間

網站間 (S2S) 連線是透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道建立的連線。此類型的連線需要位於內部部署的 VPN 裝置，其具有指派的公用 IP 位址且不是位於 NAT 後方。S2S 連線可以用於跨單位與混合式組態。

![S2S 連線](./media/vpn-gateway-about-vpngateways/demos2s.png "站對站")


### 多網站

您可以設定及設定您的 VNet 與多個內部部署網路之間的 VPN 連線。處理多重連線時，您必須使用路由式 VPN 類型 (適用於傳統 VNet 的動態閘道)。因為 VNet 只能有一個虛擬網路閘道，而透過該閘道的所有連線會共用可用的頻寬。這種組態通常稱為「多站台」連線。
 

![多站台連線](./media/vpn-gateway-about-vpngateways/demomulti.png "多站台")

### 部署模型和方法

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet 對 VNet

將虛擬網路連接至另一個虛擬網路 (VNet 對 VNet)，類似於將 VNet 連接至內部部署網站位置。這兩種連線類型都使用 Azure VPN 閘道提供使用 IPsec/IKE 的安全通道。您甚至可以將多網站組態與 VNet 對 VNet 通訊結合。這可讓您建立結合了跨單位連線與內部虛擬網路連線的網路拓撲。

您所連接的 VNet 可以：

- 在相同或不同的區域中
- 在相同或不同的訂用帳戶中
- 在相同或不同的部署模型中



![VNet 對 VNet 連線](./media/vpn-gateway-about-vpngateways/demov2v.png "VNet 對 VNet")



### 部署模型之間的連線

Azure 目前有兩種部署模型：傳統和 Resource Manager。如果您已使用 Azure 一段時間，則可能具有傳統 VNet 上執行的 Azure VM 和執行個體角色。較新的 VM 和角色執行個體可能會在 Resource Manager 中建立的 VNet 中執行。您可以建立 Vnet 間的連線，讓其中一個 VNet 中的資源直接與另一個 VNet 中的資源通訊。

### VNet 對等互連

只要您的虛擬網路組態符合特定需求，您就能夠使用 VNet 對等互連來建立您的連線。VNet 對等互連不會使用虛擬網路閘道。[VNET 對等互連](../virtual-network/virtual-network-peering-overview.md)目前處於預覽狀態。


### 部署模型和方法

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## 點對站

點對站 (P2S) 組態可讓您建立從個別的用戶端電腦到您的虛擬網路的安全連線。P2S 是透過 SSTP (安全通訊端通道通訊協定) 的 VPN 連線。P2S 連線不需要 VPN 裝置或公眾對應 IP 位址即可運作。您可從用戶端電腦啟動 VPN 連線，以建立 VPN 連線。當您想要從遠端位置 (例如從住家或會議) 連接到您的 VNet 時，或您只有幾個需要連線至 VNet 的用戶端時，這是很實用的解決方案。


![點對站連線](./media/vpn-gateway-about-vpngateways/demop2s.png "點對站")

### 部署模型和方法

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

如需 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。


## 站對站及 ExpressRoute 並存連線

ExpressRoute 是從 WAN (不透過公用網際網路) 至 Microsoft 服務 (包括 Azure) 的專用連線。站對站 VPN 流量會以加密方式透過公用網際網路進行傳輸。能夠對相同的虛擬網路設定網站間 VPN 和 ExpressRoute 連線有諸多好處。您可以將網站間 VPN 設定為 ExpressRoute 的安全容錯移轉路徑，或使用網站間 VPN 來連線至不在您網路中但透過 ExpressRoute 連接的網站。


![並存連線](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### 部署模型和方法

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## 後續步驟

如需有關 VPN 閘道的詳細資訊，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)。

將內部部署位置連接到 VNet。請參閱[建立網站間連線](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。





 

<!---HONumber=AcomDC_0824_2016-->