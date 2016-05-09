<properties 
   pageTitle="規劃與設計 VPN 閘道| Microsoft Azure"
   description="深入了解跨單位、混合式和 VNet 對 VNet 連線的 VPN 閘道規劃與設計"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/28/2016"
   ms.author="cherylmc"/>

# 規劃與設計 VPN 閘道

根據您的網路需求而定，跨單位及 VNet 對 VNet 連線的規劃與設計作業有可能很簡單，也可能很複雜。

## 規劃


### <a name="compare"></a>1.比較跨單位連線的選項

如果您決定要安全地將內部部署網站連接到虛擬網路，可使用下列三種不同方法：站對站、點對站和 ExpressRoute。比較可使用的不同跨單位連線。您選擇的選項可能取決於各種不同的考量，例如：


- 您的方案需要哪種輸送量?
- 您想讓通訊透過經由安全 VPN 的公用網際網路或透過私人連線?
- 您有可供使用的公用 IP 位址嗎?
- 您打算使用 VPN 裝置嗎? 如果是，它相容嗎？
- 您只連線幾台電腦或您想要網站持續連線?
- 您想要建立的方案需要什麼類型的 VPN 閘道?
- 您應該使用哪一種閘道 SKU？


下表可以協助您為方案決定最佳的連線選項。


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>2.請依據 VPN 類型和 SKU 來考慮閘道需求


在建立 VPN 閘道時，您必須指定想要使用的閘道 SKU。有 3 種 VPN 閘道 SKU：

- 基本
- 標準
- 高效能

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]



### 3\.閘道類型和彙總輸送量估計值

下方資料表顯示閘道類型，以及估計的彙總輸送量。估計的彙總輸送量可能是您的設計決定因素。閘道 SKU 之間的定價並不相同。如需定價資訊，請參閱＜[VPN 閘道定價](https://azure.microsoft.com/pricing/details/vpn-gateway/)＞。此資料表適用於資源管理員與傳統部署模型。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]



### <a name="wf"></a>4.工作流程

下列清單列出常見的雲端連線工作流程：

1.	設計和規劃連線的拓撲，列出所有您想要連接的網路位址空間。
2.	建立 Azure 虛擬網路。 
3.	建立虛擬網路的 VPN 閘道。
4.	建立及設定連接至內部網路或其他虛擬網路的連線 (視需要)。
5.	建立並設定 Azure VPN 閘道的點對站連線 (視需要)。
 

## 設計

### 1\.選取連線拓撲

先來看看[連線拓撲](vpn-gateway-topology.md)文章。本文包含基本圖表、每種拓撲的部署模型 (Resource Manager 或傳統)，並說明您可以使用哪種部署工具來部署組態。

### 2\.了解設計基本概念

下列各節將討論 VPN 閘道的基本概念。此外，您也需要將[網路服務限制](../articles/azure-subscription-service-limits.md#networking-limits)納入考量。


#### <a name="subnets"></a>關於子網路

在規劃和設計最適合您環境的連線時，務必要將可以使用的 IP 位址範圍和子網路納入考量。

您必須為 VNet 建立閘道子網路以設定 VPN 閘道。所有閘道子網路都必須命名為 GatewaySubnet 才能正常運作。因此，請不要將閘道子網路命名為不同的名稱，也不要將 VM 或任何其他項目部署至閘道子網路。如需閘道子網路的詳細資訊，請參閱＜關於 VPN 閘道＞文章中的[閘道子網路](vpn-gateway-about-vpngateways.md#gwsub)一節。

建立連線時，在許多情況下，您必須注意連線之間重疊的子網路位址範圍。當一個虛擬網路或內部部署位置包含的位址空間與其他位置重複時，就會發生子網路重疊的情況。這表示您需要請本機內部部署網路的網路工程師規劃範圍，以供您使用 Azure IP 位址空間/子網路。您需要的位址空間是本機內部部署網路不會用到的位址空間。

使用 VNet 對 VNet 連線時，也要注意避免重疊的子網路。如果子網路重疊且傳送和目的地 Vnet 的 IP 位址重複，就無法建立 VNet 對 VNet 連線。在此情況下，因為目的地位址是傳送 VNet 的一部分，Azure 就無法將資料傳送至另一個 VNet。



#### <a name="local"></a>關於區域網路閘道

區域網路閘道通常是指您的內部部署位置。在傳統部署模型中，區域網路閘道被稱為本機站台。您需指定區域網路閘道的名稱 (即內部部署 VPN 裝置的公用 IP 位址)，並指定位於內部部署位置的位址首碼。Azure 會查看網路流量的目的地位址首碼、查閱您為區域網路閘道指定的組態，並據以路由傳送封包。您可以視需要修改這些位址首碼。如需區域網路閘道的詳細資訊，請參閱＜關於 VPN 閘道＞文章中的[區域網路閘道](vpn-gateway-about-vpngateways.md#lng)一節。


#### <a name="gwtype"></a>關於 VPN 閘道類型

為拓撲選取正確的閘道類型相當重要。如果您選取錯誤的類型，您的閘道將無法正常運作。閘道類型會指定閘道本身如何連接以及為何它是 Resource Manager 部署模型的必要組態設定。

閘道類型如下：

- Vpn
- ExpressRoute

#### 關於連線類型

每個組態皆需要特定的連線類型。連線類型如下：

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>關於 VPN 類型

每個組態皆需要特定 VPN 類型才能運作。如果您要結合兩個組態，例如建立連往相同 VNet 的站對站連線和點對站連線，您必須使用同時符合這兩個連線需求的 VPN 類型。在點對站和站對站並存連線的情況下，使用 Azure Resource Manager 部署模型時必須使用路由式 VPN 類型，若使用傳統部署模式則必須使用動態閘道。

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

下表顯示每種連線設定對應的 VPN 類型。請確定閘道的 VPN 類型符合您想要建立的組態。


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>3.選取站對站連線的 VPN 裝置

不論部署模型為何，在設定站對站連線時，您都需要下列項目︰

- 與 Azure VPN 閘道相容的 VPN 裝置
- 不在 NAT 後方的公開 IPv4 IP 位址

您應具備設定 VPN 裝置的經驗，才能建立站對站組態。如需 VPN 裝置的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。VPN 裝置文章包含已驗證的裝置、尚未驗證的裝置需求，以及每個裝置的裝置設定文件連結的相關資訊 (如果有的話)。

### <a name="forcedtunnel"></a>4.請考慮強制通道路由

對於多數組態，您可以設定強制通道。強制通道可讓您透過站對站 VPN 通道，重新導向或「強制」所有網際網路繫結流量傳回內部部署位置，以便進行檢查和稽核。這是多數企業 IT 原則的重要安全性需求。

若不使用強制通道，則 Azure 中來自 VM 的網際網路繫結流量會永遠從 Azure 網路基礎結構直接向外周遊到網際網路，而您無法選擇檢查或稽核流量。未經授權的網際網路存取可能會導致資訊洩漏或其他類型的安全性漏洞。如需設定強制通道的詳細資訊，請參閱[有關傳統部署模型的強制通道](vpn-gateway-about-forced-tunneling.md)和[有關 Resource Manager 部署模型的強制通道](vpn-gateway-about-forced-tunneling.md)。

**強制通道圖表**

![強制通道連線](./media/vpn-gateway-plan-design/forced-tunnel.png "強制通道")


此表格列出強制通道適用的部署模型、可用來設定強制通道的部署工具，以及相關文章的直接連結 (如果有的話)。當有新文章可供您使用時，我們會頻繁更新這些資料表。

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]



## 後續步驟

如需有助您進行設計的詳細資訊，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)和[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。如需連線拓撲的詳細資訊，請參閱[連線拓撲](vpn-gateway-topology.md)。

<!---HONumber=AcomDC_0427_2016-->