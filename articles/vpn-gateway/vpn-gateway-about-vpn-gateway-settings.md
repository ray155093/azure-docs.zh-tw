<properties 
   pageTitle="關於 VPN 閘道設定 | Microsoft Azure"
   description="了解 Azure 虛擬網路的 VPN 閘道設定。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="cherylmc" />

# 關於 VPN 閘道設定

VPN 閘道是用來在虛擬網路和內部部署位置之間傳送網路流量之設定的集合。您也可以使用「VPN 閘道」來傳送 Azure 內 VNet 之間的流量。本文各節將討論與 VPN 閘道相關的設定。

有時，使用連線圖會有助於檢視可用的組態。您可以在文章的[關於 VPN 閘道連線](vpn-gateway-topology.md)一節中，找到說明如何部署每個組態的圖表。


## <a name="gwsku"></a>閘道 SKU

在建立 VPN 閘道時，您必須指定想要使用的閘道 SKU。閘道 SKU 適用於 ExpressRoute 和 VPN 閘道類型。閘道 SKU 之間的定價並不相同。如需定價資訊，請參閱＜[VPN 閘道定價](https://azure.microsoft.com/pricing/details/vpn-gateway/)＞。如需 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。

有 3 種 VPN 閘道 SKU：

- 基本
- 標準
- HighPerformance

下列 PowerShell 範例將 `-GatewaySku` 指定為 *Standard*。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased


###  <a name="aggthroughput"></a>依 SKU 和閘道類型列出的估計彙總輸送量


下表顯示閘道類型和預估的彙總輸送量。此資料表適用於資源管理員與傳統部署模型。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>閘道類型

閘道類型會指定閘道的連線方式，並且是 Resource Manager 部署模型的必要組態設定。請勿將閘道類型與 VPN 類型混為一談，後者所指定的是 VPN 的路由類型。`-GatewayType` 的可用值為：

- Vpn
- ExpressRoute


這個 Resource Manager 部署模型的 PowerShell 範例將 -GatewayType 指定為 *Vpn*。在建立閘道時，您必須確定用於組態的閘道類型是正確的。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased

## <a name="connectiontype"></a>連線類型

每個組態皆需要特定的連線類型。`-ConnectionType` 的可用 Resource Manager PowerShell 值為：

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

在下列 PowerShell 範例中，我們會建立需要 "IPsec" 連線類型的 S2S 連線。

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>VPN 類型

每個組態皆需要特定 VPN 類型才能運作。在建立組態時，您需選取連線所需的 VPN 類型。

例如，如果您想要既使用站對站 (S2S) 也使用點對站 (P2S) 來連線到 VNet，就必須使用可同時滿足這兩種組態之連線需求的 VPN 類型。P2S 連線需要路由型 VPN 類型 (動態路由閘道)，而 S2S 連線則有時可以支援原則型 VPN 類型。這表示如果您已經有使用原則型 VPN 類型 (靜態路由) 的 S2S 連線，您就必須使用路由型 VPN 類型 (動態路由) 來重新建立閘道，才能支援 P2S。

有兩種 VPN 類型：

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]



這個 Resource Manager 部署模型的 PowerShell 範例將 `-VpnType` 指定為 *RouteBased*。在建立閘道時，您必須確定用於組態的 -VpnType 是正確的。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>閘道需求

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>閘道子網路

若要設定 VPN 閘道，您必須先為 VNet 建立閘道子網路。此閘道子網路必須命名為 *GatewaySubnet* 才能正常運作。此名稱可讓 Azure 知道這個子網路應該用於閘道。<BR>如果您使用傳統入口網站，閘道子網路在入口網站介面中會自動命名為「閘道」。這專用於在傳統入口網站中檢視閘道子網路。在此情況下，實際上是以 *GatewaySubnet* 值建立子網路，並可在 Azure 入口網站與 PowerShell 中以此方式檢視。

閘道子網路的大小下限完全取決於您想要建立的組態。雖然可以建立像 /29 這麼小的閘道子網路，但建議您建立 /28 或更大 (/28、/27、/26 等) 的閘道子網路。

建立較大的閘道可防止違反閘道大小限制。例如，您可能已建立一個虛擬網路閘道，其中用於 S2S 連線的閘道子網路大小為 /29。您現在想要設定一個 S2S/ExpressRoute 並存組態。該組態要求閘道子網路大小至少為 /28。若要建立您的組態，您將必須修改閘道子網路以配合該連線的最低需求，亦即 /28。

下列 PowerShell 範例顯示名為 GatewaySubnet 的閘道子網路。您可以看到 CIDR 標記法指定 /27，這可提供足以供大多數現有組態使用的 IP 位址。

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] 請確認閘道子網路並未套用「網路安全性群組」(NSG)，因為這可能導致連線失敗。


## <a name="lng"></a>區域網路閘道

區域網路閘道通常是指您的內部部署位置。在傳統部署模型中，區域網路閘道被稱為本機站台。您需指定區域網路閘道的名稱 (即內部部署 VPN 裝置的公用 IP 位址)，並指定位於內部部署位置的位址首碼。Azure 會查看網路流量的目的地位址首碼、查閱您為區域網路閘道指定的組態，然後根據這些來路由傳送封包。

有時，您會需要修改區域網路閘道設定。例如，當您新增或修改位址範圍時，或 VPN 裝置的 IP 位址變更時。針對傳統 VNet，您可以在傳統入口網站的 [區域網路] 頁面上變更這些設定。針對 Resource Manager，請參閱[使用 PowerShell 修改區域網路閘道設定](vpn-gateway-modify-local-network-gateway.md)。

## <a name="resources"></a>REST API 和 PowerShell

如需其他技術資源及使用 REST API 和 PowerShell 時的特定語法需求，請參閱下列頁面︰

|**傳統** | **資源管理員**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## 後續步驟

如需有關可用連線組態的詳細資訊，請參閱 [VPN 閘道連線](vpn-gateway-topology.md)。







 

<!---HONumber=AcomDC_0824_2016-->