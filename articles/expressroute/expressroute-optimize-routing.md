<properties
   pageTitle="最佳化 ExpressRoute 路由 | Microsoft"
   description="此頁面提供當客戶有一個以上的 ExpressRoute 線路連接 Microsoft 與客戶的公司網路時，如何最佳化路由的詳細資訊。"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/26/2016"
   ms.author="charwen"/>

# 最佳化 ExpressRoute 路由
當您有多個 ExpressRoute 線路時，會有一個以上的路徑來連線到 Microsoft。因此，可能會產生次佳的路由 - 也就是，您的流量可能會經由較長的路徑連到 Microsoft，而 Microsoft 也可能會經由較長的路徑連到您的網路。網路路徑愈常，延遲愈久。延遲對於應用程式效能和使用者體驗有直接的影響。本文將說明這個問題，並說明如何使用標準路由技術來最佳化路由。

## 次佳路由案例 1
讓我們依照範例仔細觀察路由問題。假設您在美國有兩個辦公室，一個在洛杉磯，一個在紐約。您的辦公室是在廣域網路 (WAN) 上連線，該網路可以是您自己的骨幹網路或服務提供者的 IP VPN。您有兩個也是在 WAN 上連線的 ExpressRoute 線路，一個在美國西部，一個在美國東部。很明顯地，您有兩個路徑可連線到 Microsoft 網路。現在假設您在美國西部和美國東部均有 Azure 部署 (例如 Azure App Service)。您的用意是要將洛杉磯的使用者連接到 Azure 美國西部以及將紐約的使用者連接到 Azure 美國東部，因為您的服務系統管理員告知每個辦公室的使用者存取附近的 Azure 服務以獲得最佳的體驗。不幸的是，此計畫比較適合用於東岸的使用者，但不適用於西岸的使用者。此問題的原因如下所示。在每個 ExpressRoute 線路上，我們會告知您 Azure 美國東部的前置詞 (23.100.0.0/16)和 Azure 美國西部的前置詞 (13.100.0.0/16)。如果您不知道哪個前置詞來自哪個區域，您就無法將它視為不同。您的 WAN 網路可能會認為這兩個前置詞比較接近美國東部 (相較於美國西部)，因此將兩個辦公室的使用者路由至美國東部的 ExpressRoute 線路。最後，洛杉磯辦公室會有許多使用者不太滿意。

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### 解決方法︰使用 BGP 社群
若要為這兩個辦公室的使用者最佳化路由，您需要知道哪個前置詞來自 Azure 美國西部以及哪個前置詞來自 Azure 美國東部。我們使用 [BGP 社群值](expressroute-routing.md)來編碼這項資訊。我們已將唯一的 BGP 社群值指派給每個 Azure 區域，例如"12076:51004" 適用於美國東部，"12076:51006" 適用於美國西部。您現在知道哪個前置詞來自哪個 Azure 區域，即可設定應優先使用哪個 ExpressRoute 線路。因為我們使用 BGP 來交換路由資訊，您可以使用 BGP 的本機喜好設定來影響路由。在我們的範例中，您可以指派比美國東部還要高的本機喜好設定值給美國西部的 13.100.0.0/16，同樣地，指派比美國西部還要高的本機喜好設定值給美國東部的 23.100.0.0/16。此組態可確保當Microsoft 的兩個路徑都可用時，洛杉磯的使用者將經由美國西部的 ExpressRoute 線路連接到 Azure 美國西部，而紐約的使用者會經由美國東部的 ExpressRoute 線路連接到 Azure 美國東部。這兩端的路由均已最佳化。

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## 次佳路由案例 2
在下面的另一個範例中，來自 Microsoft 的連線經由較長的路徑連到您的網路。在此情況下，您可在[混合式環境](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx)中使用內部部署 Exchange 伺服器和 Exchange Online。您的辦公室已連線到 WAN。您告知兩個辦公室中內部部署伺服器的前置詞，以透過兩個 ExpressRoute 線路連到 Microsoft。在信箱移轉的情況下，Exchange Online 會起始對內部部署伺服器的連線。不幸的是，在橫越整個大陸回到西岸之前，洛杉磯辦公室的連線會路由至美國東部的 ExpressRoute 線路。此問題的原因類似第一個問題。沒有任何提示，Microsoft 網路無法分辨哪個客戶前置詞接近美國東部，哪個接近美國西部。有可能會對洛杉磯辦公室挑選錯誤的路徑。

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### 解決方案︰使用 AS PATH 前置
這個問題有兩個解決方案。第一個解決方案是您只需告知在美國西部 ExpressRoute 線路上洛杉磯辦公室的內部部署前置詞 (177.2.0.0/31)，以及美國東部 ExpressRoute 線路上紐約辦公室的內部部署前置詞 (177.2.0.2/31)。如此一來，只有一個路徑可供 Microsoft 連接到每個辦公室。沒有模稜兩可的狀況，而且路由已最佳化。利用這個設計，您需要考慮您的容錯移轉策略。在經由 ExpressRoute 的 Microsoft 路徑已損毀的事件中，您需要確定 Exchange Online 仍可連線至內部部署伺服器。

第二個解決方案是您繼續告知兩個 ExpressRoute 線路上的兩個前置詞，此外請提供哪個前置詞接近哪個辦公室的提示。因為我們支援 BGP AS PATH 前置，所以您可以設定前置詞的 AS PATH 來影響路由。在此範例中，您可以延長美國東部 172.2.0.0/31 的 AS PATH，以致我們偏好將美國西部的 ExpressRoute 線路用於以此前置詞為目的地的流量 (因為我們的網路會認為此前置詞的路徑在西部比較短)。同樣地，您可以延長美國西部 172.2.0.2/31 的 AS PATH，以致我們偏好美國東部的 ExpressRoute 線路。這兩個辦公室的路由均已最佳化。採用這個設計，如果一個 ExpressRoute 路線已中斷，Exchange Online 仍可透過另一個 ExpressRoute 線路和您的 WAN 來觸達您。

>[AZURE.IMPORTANT] 我們會針對在 Microsoft 對等上收到的前置詞，移除 AS PATH 中的私用 AS 編號。您必須在 AS PATH 中附加公用 AS 編號，才能影響 Microsoft 對等的路由。

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

<!---HONumber=AcomDC_0601_2016-->