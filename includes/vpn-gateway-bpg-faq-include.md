### 所有的 Azure VPN 閘道 SKU 上是否都支援 BGP？

否，在 Azure **標準**和**HighPerformance** VPN 閘道上才支援 BGP。不支援 **基本** SKU。

### 可以使用 BGP 與 Azure Policy-Based VPN 閘道嗎？

否，僅路由 VPN 閘道支援 BGP。

### 可以使用私人 ASN (自發系統編號) 嗎？

是，針對您的內部部署網路和 Azure 虛擬網路，您可以使用您自己的公用 ASN 或私人 ASN。

### 內部部署 VPN 網路和 Azure VNet 可以使用相同的 ASN 嗎？

否，如果您要將內部部署網路和 Azure VNet 與 BGP 連接，必須在內部部署網路與 Azure VNet 之間指派不同 ASN。Azure VPN 閘道已指派 65515 的預設 ASN，無論是否已針對跨單位連線啟用 BGP。您可以在建立 VPN 閘道時指派不同的 ASN 來覆寫這個預設值，或在建立閘道之後變更 ASN。您必須將內部部署 ASN 指派給對應 Azure 區域網路閘道。



### Azure VPN 閘道會通告我哪些位址首碼？

Azure VPN 閘道會通告下列路由至您的內部部署 BGP 裝置︰

- 您的 VNet 位址首碼
- 每個本機網路閘道的位址首碼已連線到 Azure VPN 閘道
- 從其他 BGP 對等互連工作階段識別的路由已連線到 Azure VPN 閘道，**除了預設路由或與任何 VNet 首碼重疊的路由**。

### 可以與我的 VNet 對 VNet 連線搭配使用 BGP 嗎？

是，您可以針對跨單位連線和 VNet 對 VNet 連線使用 BGP。

### 可以針對我的 Azure VPN 閘道混合使用 BGP 和非 BGP 連線嗎？

是，針對相同的 Azure VPN 閘道，您可以混合使用 BGP 和非 BGP 連線。

### Azure VPN 閘道是否支援 BGP 傳輸路由？

是，可支援 BGP 傳輸路由，但例外狀況為 Azure VPN 閘道**不會**通告預設路由至其他的 BGP 對等互連。若要啟用跨多個 Azure VPN 閘道的路由傳輸，您必須在所有中繼 VNet 對 VNet 連線上啟用 BGP。

### 是否可在 Azure VPN 閘道與我的內部部署網路之間擁有多個通道？

是，您可在 Azure VPN 閘道與內部部署網路之間建立多個 S2S VPN 通道。請注意，所有這些通道將會計入您 Azure VPN 閘道的通道總數。例如，如果您在 Azure VPN 閘道與其中一個內部部署網路之間有兩個備援通道，它們會在您的 Azure VPN 閘道的總配額 (標準為 10，HighPerformance 為 30) 中耗用 2 個通道。

### 可以在兩個具有 BGP 的 Azure VNet 之間擁有多個通道嗎？

否，不支援一對虛擬網路之間的備援通道。

### 我可以在 ExpressRoute/S2S VPN 共存組態中使用適用於 S2S VPN 的 BGP 嗎？

目前沒有。

### Azure VPN 閘道會對 BGP 對等互連 IP 使用什麼位址？

Azure VPN 閘道會配置針對虛擬網路定義的 GatewaySubnet 範圍的單一 IP 位址。根據預設，它是範圍的最後第二個位址。例如，如果您的 GatewaySubnet 是 10.12.255.0.0/27，範圍從 10.42.255.0.0 到 10.42.255.31，則 Azure VPN 閘道上的 BGP 對等互連 IP 位址會是 10.12.255.30。當您列出 Azure VPN 閘道器資訊時，可以找到這項資訊。

### 我的 VPN 裝置上的 BGP 對等互連 IP 位址有哪些需求？

您內部部署 BGP 對等互連位址**不得**與您的 VPN 裝置的公用 IP 位址相同。在 VPN 裝置上針對 BGP 對等互連 IP 使用不同的 IP 位址。它可以是指派給裝置上的回送介面的位址。在代表位置的對應本機網路閘道中指定這個位址。

### 使用 BGP 時，應將區域網路閘道的位址首碼指定為什麼？

Azure 區域網路閘道會指定內部部署網路的起始位址首碼。若具有 BGP，您必須配置 BGP 對等互連 IP 位址的主機首碼 (/32 首碼) 作為該內部部署網路的位址空間。如果 BGP 對等互連 IP 為 10.52.255.254，您應該指定「10.52.255.254/32」作為代表此內部部署網路的區域網路閘道的 localNetworkAddressSpace。這是為了確保 Azure VPN 閘道透過 S2S VPN 通道建立 BGP 工作階段。

### 我應該將什麼加入我的 BGP 對等互連工作階段的內部部署 VPN 裝置？

您應該在指向 IPsec S2S VPN 通道的 VPN 裝置上加入 Azure BGP 對等互連 IP 位址的主機路由。例如，如果 Azure VPN 對等互連 IP 是「10.12.255.30」，您應該加入 VPN 裝置上具有比對 IPsec 通道介面的躍點介面的「10.12.255.30」主機路由。