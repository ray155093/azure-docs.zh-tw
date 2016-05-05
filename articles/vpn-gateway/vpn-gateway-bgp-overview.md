<properties
   pageTitle="BGP 與 Azure VPN 閘道概觀 | Microsoft Azure"
   description="本文提供 BGP 與 Azure VPN 閘道的概觀。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/26/2016"
   ms.author="yushwang"/>

# BGP 與 Azure VPN 閘道概觀

這篇文章提供 Azure VPN 閘道中的 BGP (邊界閘道協定) 支援概觀。

## 有關 BGP

BGP 是常用於網際網路的標準路由通訊協定，可交換兩個或多個網路之間的路由和可執行性資訊。在 Azure 虛擬網路的內容中使用時，BGP 會啟用 Azure VPN 閘道，以及內部部署 VPN 裝置 (稱為 BGP 對等互連或鄰近項目) 來交換「路由」，其會通知這兩個閘道對要通過閘道的首碼或所涉及之路由器的可用性和可執行性。BGP 也可以傳播從一個 BGP 對等互連到所有其他 BGP 對等所識別的 BGP 閘道，來啟用多個網路之間的傳輸路由。
 
### 為何要使用 BGP？

BGP 是選用功能，可供您與 Azure 路由 VPN 閘道搭配使用。您也應該要先確定您的內部部署 VPN 裝置支援 BGP 後，再啟用此功能。您可以無需 BGP 即可繼續使用 Azure VPN 閘道和您的內部部署 VPN 裝置。它相當於使用靜態路由 (不含 BGP) 與在您的網路和 Azure 之間使用具有 BGP 的動態路由。

BGP 具有數個優點和新功能：

#### 支援自動和彈性的前置詞更新

利用 BGP，您只需要透過 IPsec S2S VPN 通道宣告特定 BGP 對等互連的最小前置詞。它也可以小至內部部署 VPN 裝置的 BGP 對等互連 IP 位址的主機前置詞 (/ 32)。您可以控制您要公告至 Azure 以允許 Azure 虛擬網路存取的內部部署網路首碼。
	
您也可以公告可能包含一些您的 VNet 位址首碼的較大首碼，例如預設路由 (0.0.0.0/0) 或大型私人 IP 位址空間 (例如 10.0.0.0/8)。可是請注意，首碼無法與您的任何一個 VNet 首碼相同。與您的 VNet 首碼相同的路由將會遭到拒絕。

#### 根據 BGP 使用自動容錯移轉，在 VNet 和內部部署站台之間支援多個通道

您可以在 Azure VNet 與內部部署 VPN 裝置之間的相同位置中建立多個連接。這項功能在主動-主動組態中的兩個網路之間提供多個通道 (路徑)。如果其中一個通道已中斷連接，對應的路由會透過 BGP 撤回，且流量會自動轉換到其餘的通道。
	
下圖顯示這項高可用性設定的簡單範例︰
	
![多個作用中路徑](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### 支援內部部署網路與多個 Azure Vnet 之間的傳輸路由

BGP 可讓多個閘道識別及傳播來自不同網路的首碼，無論這些網路是直接或間接連線。這可以使用內部部署站台之間的 Azure VPN 閘道，或跨多個 Azure 虛擬網路啟用傳送路由。
	
下圖顯示具有多個路徑的多重躍點拓撲的範例，這些路徑可透過Microsoft 網路內的 Azure VPN 閘道來傳輸兩個內部部署網路之間的流量︰

![多重躍點傳輸](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## BGP 常見問題集

#### 所有的 Azure VPN 閘道 SKU 上是否都支援 BGP？

否，在 Azure **標準**和**HighPerformance** VPN 閘道上才支援 BGP。**基本** SKU 不提供支援。

#### 可以使用 BGP 與 Azure Policy-Based VPN 閘道嗎？

否，僅路由 VPN 閘道支援 BGP。

#### 可以使用私人 ASN (自發系統編號) 嗎？

是，針對您的內部部署網路和 Azure 虛擬網路，您可以使用您自己的公用 ASN 或私人 ASN。

#### 內部部署 VPN 網路和 Azure VNet 可以使用相同的 ASN 嗎？

否，如果您要將內部部署網路和 Azure VNet 與 BGP 連接，必須在內部部署網路與 Azure VNet 之間指派不同 ASN。Azure VPN 閘道已指派 65515 的預設 ASN，無論是否已針對跨單位連線啟用 BGP。您可以在建立 VPN 閘道時指派不同的 ASN 來覆寫這個預設值，或在建立閘道之後變更 ASN。您必須將內部部署 ASN 指派給對應 Azure 區域網路閘道。



#### Azure VPN 閘道會通告我哪些位址首碼？

Azure VPN 閘道會通告下列路由至您的內部部署 BGP 裝置︰

- 您的 VNet 位址首碼
- 每個本機網路閘道的位址首碼已連線到 Azure VPN 閘道
- 從其他 BGP 對等互連工作階段識別的路由已連線到 Azure VPN 閘道，**除了預設路由或與任何 VNet 首碼重疊的路由**。

#### 可以與我的 VNet 對 VNet 連線搭配使用 BGP 嗎？

是，您可以針對跨單位連線和 VNet 對 VNet 連線使用 BGP。

#### 可以針對我的 Azure VPN 閘道混合使用 BGP 和非 BGP 連線嗎？

是，針對相同的 Azure VPN 閘道，您可以混合使用 BGP 和非 BGP 連線。

#### Azure VPN 閘道是否支援 BGP 傳輸路由？

是，可支援 BGP 傳輸路由，但例外狀況為 Azure VPN 閘道**不會**通告預設路由至其他的 BGP 對等互連。若要啟用跨多個 Azure VPN 閘道的路由傳輸，您必須在所有中繼 VNet 對 VNet 連線上啟用 BGP。

#### 是否可在 Azure VPN 閘道與我的內部部署網路之間擁有多個通道？

是，您可在 Azure VPN 閘道與內部部署網路之間建立多個 S2S VPN 通道。請注意，所有這些通道將會計入您 Azure VPN 閘道的通道總數。例如，如果您在 Azure VPN 閘道與其中一個內部部署網路之間有兩個備援通道，它們會在您的 Azure VPN 閘道的總配額 (標準為 10，HighPerformance 為 30) 中耗用 2 個通道。

#### 可以在兩個具有 BGP 的 Azure VNet 之間擁有多個通道嗎？

否，不支援一對虛擬網路之間的備援通道。

#### Azure VPN 閘道會對 BGP 對等互連 IP 使用什麼位址？

Azure VPN 閘道會配置針對虛擬網路定義的 GatewaySubnet 範圍的單一 IP 位址。根據預設，它是範圍的最後第二個位址。例如，如果您的 GatewaySubnet 是 10.12.255.0.0/27，範圍從 10.42.255.0.0 到 10.42.255.31，則 Azure VPN 閘道上的 BGP 對等互連 IP 位址會是 10.12.255.30。當您列出 Azure VPN 閘道器資訊時，可以找到這項資訊。

#### 我的 VPN 裝置上的 BGP 對等互連 IP 位址有哪些需求？

您內部部署 BGP 對等互連位址**不得**與您的 VPN 裝置的公用 IP 位址相同。在 VPN 裝置上針對 BGP 對等互連 IP 使用不同的 IP 位址。它可以是指派給裝置上的回送介面的位址。在代表位置的對應本機網路閘道中指定這個位址。

#### 使用 BGP 時，應將區域網路閘道的位址首碼指定為什麼？

Azure 區域網路閘道會指定內部部署網路的起始位址首碼。若具有 BGP，您必須配置 BGP 對等互連 IP 位址的主機首碼 (/32 首碼) 作為該內部部署網路的位址空間。如果 BGP 對等互連 IP 為 10.52.255.254，您應該指定「10.52.255.254/32」作為代表此內部部署網路的區域網路閘道的 localNetworkAddressSpace。這是為了確保 Azure VPN 閘道透過 S2S VPN 通道建立 BGP 工作階段。

#### 我應該將什麼加入我的 BGP 對等互連工作階段的內部部署 VPN 裝置？

您應該在指向 IPsec S2S VPN 通道的 VPN 裝置上加入 Azure BGP 對等互連 IP 位址的主機路由。例如，如果 Azure VPN 對等互連 IP 是「10.12.255.30」，您應該加入 VPN 裝置上具有比對 IPsec 通道介面的躍點介面的「10.12.255.30」主機路由。

## 後續步驟

請參閱 [開始使用 Azure VPN 閘道上的 BGP](./vpn-gateway-bgp-resource-manager-ps.md)，以了解設定跨單位與 VNet 對 VNet 連線 BGP 的步驟。

<!---HONumber=AcomDC_0427_2016-->