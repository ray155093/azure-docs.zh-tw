<properties
   pageTitle="非對稱式路由 | Microsoft Azure"
   description="本文逐步解說當客戶的網路中有多個連結連至一個目的地時，客戶可能會面對的非對稱式路由問題。"
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"/>

# 非對稱式路由與多個網路路徑

本文說明如何當來源與目的地之間有多個路徑時，向前和回傳流量如何採取不同的路由。

若要了解非對稱式路由，我們需要了解兩個概念。一個是多個網路路徑的影響。另一個則是保存狀態的裝置 (如防火牆) 行為。這些裝置稱為具狀態裝置。這兩個因素的組合可產生流量會隨著具狀態裝置下降的案例，因為它看不到透過其本身所產生的流量。

## 多個網路路徑

當企業網路只有一個透過其網際網路服務提供者連至網際網路的連結時，則往返網際網路的所有流量都是透過相同的路徑傳送。公司通常會購買多個線路，做為備援路徑，以改善網路運作時間。在這類情況下，離開網路而朝向網際網路的流量可能會通過一個連結，而回傳流量則是透過不同的連結傳送。這種現象通常稱為非對稱式路由，其中的反向傳輸會取用與原始流程不同的路徑。

![Routing3](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

雖然上述說明適用於網際網路，但會套用至其他多個路徑的組合。舉例來說，目的地相同的網際網路路徑和私用路徑、目的地相同的多個私用路徑等。

從來源一路到目的地的每個路由器會根據其到達目的地的最佳路徑計算，來計算到達目的地的最佳路徑。根據兩個主要因素決定可能最佳的路徑。

1.	外部網路之間的路由是以稱為「邊界閘道通訊協定」(通常稱為 BGP) 的路由通訊協定為基礎。BGP 會接受芳鄰的通告並經由數個步驟來執行這些通告，以決定套達目的地的最佳路徑並將它安裝於其路由表中。
2.	與路由相關聯的子網路遮罩的長度。如果收到相同 IP 位址，但不同子網路遮罩的多個通告，則通常會使用具有較長子網路遮罩的通告，因為它被視為比較明確的路由。

## 具狀態裝置

路由器會為了進行路由傳送而查看封包的 IP 標頭。不過，還有看起來更深入封包的裝置。這些裝置通常會查看 Layer4 (TCP/UDP)，或甚至是 Layer7 (應用程式層) 標頭。這些裝置不是安全性裝置，就是頻寬最佳化裝置。防火牆是具狀態裝置的常見範例。防火牆會根據各種欄位 (例如通訊協定、TCP/UDP 連接埠、URL 標頭)，允許或拒絕封包通過其介面。此封包審查會對裝置造成大量處理負載。為了改善效能，防火牆會檢查流程的第一個封包。如果允許封包，它會將流程資訊保留在其狀態資料表中。根據最初的決策會允許與此流程相關的所有後續封包。因此，當封包 (現有流程的一部分) 抵達防火牆，而防火牆沒有其先前的狀態資訊時，防火牆會捨棄此封包。

## 非對稱式路由與 ExpressRoute

當您透過 ExpressRoute 連接到 Microsoft 時，您的網路就會發生下列變更。

1.	您有 Microsoft 的多個連結。一個連結是您現有的網際網路連線，而另一個則是透過 ExpressRoute。Microsoft 一些流量可能會通過網際網路，但透過 ExpressRoute 返回，反之亦然。
2.	您會透過 ExpressRoute 收到更多明確的 IP 位址。因此，對於透過 ExpressRoute 提供的服務而言，從您的網路至 Microsoft 的流量一律偏好 ExpressRoute。

若要了解上述兩者的影響，讓我們來逐步了解一些案例。假設您只有一個連到網際網路的線路，而且您透過網際網路使用所有的 Microsoft 服務。從您的網路至 Microsoft 的來回流量會周遊相同的網際網路連結並通過防火牆。防火牆會在看到第一個封包時記錄流程，並因為此流程存在於狀態資料表中而允許傳回封包。

![Routing1](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


您現在可開啟 ExpressRoute，並透過 ExpressRoute 使用 Microsoft 所提供的服務。Microsoft 提供的所有其他服務都是透過網際網路使用。您會在連接到 ExpressRoute 的邊緣部署不同的防火牆。Microsoft 會透過 ExpressRoute，針對特定服務，向您的網路通告更多特定的前置詞。您的路由基礎結構會選擇 ExpressRoute 做為這些前置詞的慣用路徑。如果您不是透過 ExpressRoute 向 Microsoft 通告您的公用 IP 位址，Microsoft 將會透過網際網路與您的公用 IP 位址通訊。因此，從您的網路至 Microsoft 的向前流量將使用 ExpressRoute，而來自 Microsoft 的反向流量會使用網際網路。當在邊緣防火牆看到回應封包流程，在狀態資料表中找不到時，它將會捨棄回傳流量。

如果您選擇將相同的 NAT 集區使用於 ExpressRoute 和網際網路，您會發現您的望路中私人 IP 位址上的用戶端有類似的問題。Windows Update 等服務的要求將會透過網際網路輸送，因為這些服務的 IP 位址不會透過 ExpressRoute 通告。不過，回傳流量不會透過 ExpressRoute 返回。如果 Microsoft 從網際網路和 ExpressRoute 收到具有相同子網路遮罩的 IP 位址，則會偏好透過網際網路的 ExpressRoute。如果在您的網路邊緣 (面對 ExpressRoute) 的防火牆或其他具狀態裝置，沒有關於流程的任何先前資訊，它將會捨棄屬於該流程的封包。

## 非對稱式路由的解決方案

主要有兩種方式可解決非對稱式路由問題。一個是透過路由，而另一個則是透過以來源為基礎 NAT (SNAT)。

1. 路由

    - 您應該確定已向適當的 WAN 連結通告您的公用 IP 位址。例如，如果您想要將網際網路使用於驗證流量，將 ExpressRoute 使用於您的郵件流量。您就不得透過 ExpressRoute 通告您的 ADFS 公用 IP 位址。同樣地，不得向透過 ExpressRoute 接收的 IP 位址公開內部部署 ADFS 伺服器。透過 ExpressRoute 收到的路由更加明確，所以會讓 ExpressRoute 成為 Microsoft 驗證流量的慣用路徑，因而造成非對稱式路由。

    - 如果您想要使用 ExpressRoute 進行驗證，您必須確定您是透過 ExpressRoute (未經 NAT) 通告 ADFS 公用 IP 位址。如此一來，源自 Microsoft 到內部部署 ADFS 伺服器的流量會透過 ExpressRoute 輸送，而從客戶到 Microsoft 的回傳流量會使用 ExpressRoute，因為最好是透過網際網路。

2. 以來源為基礎的 NAT

	解決非對稱式路由問題的另一種方式是透過來源 NAT (SNAT)。例如，如果您尚未透過 ExpressRoute 通告內部部署 SMTP 伺服器的公用 IP 位址，則傾向於使用網際網路進行此通訊。由 Microsoft 向您的內部部署 SMTP 伺服器提出的要求會周遊網際網路。您會對內部 IP 位址的連入要求進行來源 NAT 處理。來自 SMTP 伺服器的反向流量會前往邊緣防火牆 (用來進行 NAT)，而不是 ExpressRoute。如此一來，回傳流量會透過網際網路返回。


![Routing2](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## 偵測非對稱式路由

路徑追蹤是確保流量會周遊預期路徑的最佳方式。如果您預期從內部部署 SMTP 伺服器至 Microsoft 的流量會採用網際網路路徑，則進行從 SMTP 伺服器至 Office 365 的路徑追蹤。結果會驗證流量確實離開您的網路朝向網際網路，而不是朝向 ExpressRoute。

<!---HONumber=AcomDC_0824_2016-->