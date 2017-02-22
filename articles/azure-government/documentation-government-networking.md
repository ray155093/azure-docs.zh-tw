---
title: "Azure Governmenmt 網路 | Microsoft Docs"
description: "這會提供與 Azure Government 之私人連線的功能比較和指引"
services: azure-government
cloud: gov
documentationcenter: 
author: jawalte
manager: zakramer
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/24/2017
ms.author: jawalte
translationtype: Human Translation
ms.sourcegitcommit: 8288ca2e85ec0cd1a1b53862b2d60fcb88e7be4b
ms.openlocfilehash: c6b8c27299c1bfceb045ee7d82981654f3a446d8


---
# <a name="azure-government-networking"></a>Azure Government 網路
## <a name="expressroute-private-connectivity"></a>ExpressRoute (私人連線)
ExpressRoute 已在 Azure Government 中正式推出。 如需詳細資訊 (包括合作夥伴和對等互連位置)，請參閱 [ExpressRoute 公開文件](../expressroute/index.md)。

### <a name="variations"></a>變化
ExpressRoute 已在 Azure Government 中正式推出 (GA)。 

* Government 客戶可透過專用的 Azure Government (Gov) ExpressRoute (ER) 連線來連線到實體隔離的容量
* Azure Gov 利用至少相隔 500 英哩的多個區域配對來提升可用性和耐久性 
* 根據預設，所有 Azure Gov ER 連線都設定了可支援暴增情形的主動對主動備援，並可提供多達 10 G 的電路容量 (最小為 50 MB)
* Azure Gov ER 位置可為客戶和 Azure Gov 異地備援區域提供最佳化路徑 (最短的躍點、低延遲、高效能等等)
* Azure Gov ER 私人連線不會使用、周遊或依賴網際網路
* Azure Gov 的實體和邏輯基礎結構會實際專用並加以分隔，並且會限制只有美國地區的人員才能存取
* Microsoft 擁有並負責操作 Azure Gov 區域和 Azure Gov ER Meet-Me 位置之間的所有光纖基礎結構
* Azure Gov ER 提供連往 Microsoft Azure、O365 和 CRM 雲端服務的連線

### <a name="considerations"></a>考量
有兩個基本服務會提供連往 Azure Government 的私人網路連線：VPN (一般組織為網站間) 和 ExpressRoute。

Azure ExpressRoute 可用來在 Azure Government 資料中心之間、內部部署基礎結構或共置環境中建立私人連線。 ExpressRoute 連線不經過公用網際網路，相較於一般網際網路連線更為可靠、速度更快且延遲更低。 在某些情況下，使用 ExpressRoute 連線在內部部署系統與 Azure 之間傳輸資料，可以發揮巨大的成本效益。   

透過 ExpressRoute，您在 ExpressRoute 位置上就能連線到 Azure (例如 Exchange 提供者設備)，或可以從現有的 WAN 網路直接連線到 Azure (例如網路服務提供者所提供的多協定標號交換 (MPLS) VPN)。

![替代文字](./media/azure-government-capability-private-connectivity-options.PNG)  ![替代文字](./media/government-capability-expressroute.PNG)  

若要讓網絡服務支援 Azure Government 客戶應用程式和解決方案，強烈建議實作 ExpressRoute (私人連線) 以連線到 Azure Government。 如果使用 VPN 連線，請考量下列事項︰

* 客戶應連絡其授權官員/機構以判斷是否需要私人連線或其他安全連線機制，以及找出任何要考慮的額外限制。
* 客戶應決定是否要強制讓網站間 VPN 路由經過私人連線區域。
* 客戶應透過授權的私人連線存取提供者取得 MPLS 線路或 VPN。

所有使用私人連線架構的客戶都應該驗證是否已針對客戶與 Azure Government 之閘道網路/網際網路 (GN/I) 邊緣路由器分界點的連線建立並維護適當實作。 同樣地，您的組織必須在內部部署環境與 Azure Government 的閘道網路/客戶 (GN/C) 邊緣路由器分界點之間建立網路連線。

## <a name="support-for-bgp-communities"></a>BGP 社群支援
本節提供 BGP 社群如何搭配 AzureGov 中的 ExpressRoute 使用的概觀。 Microsoft 將會公告公用和 Microsoft 對等互連路徑中的路由並為路由標記適當的社群值。 這麼做的基本原理和社群值的詳細資料如下所述。 不過，Microsoft 不接受任何標記至向 Microsoft 公告之路由的社群值。

如果您要在 AzureGov 區域內的任何一個對等互連位置透過 ExpressRoute 連接到 Microsoft，您必須能夠存取 Azure Government 界限內所有區域中的所有 Microsoft 雲端服務。 

例如，如果您連線到 Microsoft 在華盛頓特區 透過 ExpressRoute 連線將可讓您存取 AzureGov 中託管的所有 Microsoft 雲端服務。

如需位置和合作夥伴的詳細資訊，以及 AzureGov 對等互連位置的 ExpressRoute 詳細清單，請參閱 [ExpressRoute 公用文件](../expressroute/index.md)上的 [概觀] 索引標籤。

您可以購買多個 ExpressRoute 迴路。 如果擁有多個連線，您即可因為異地備援而有明顯的高可用性優勢。 具有多個 ExpressRoute 循環的情況下，您會從 Microsoft 收到同一組有關公用對等互連和 Microsoft 對等互連路徑的前置詞。 這表示您將會有多個路徑可從您的網路連到 Microsoft。 這可能會導致在您的網路中做出次佳的路由決策。 因此，您可能會遇到次佳的不同服務連線體驗。 

Microsoft 會以適當的 BGP 社群值標記透過公用對等互連和 Microsoft 對等互連公告的前置詞，表示前置詞裝載於的區域。 您可以依賴社群值來做出適當的路由決策，以提供最佳路由給客戶。  如需其他詳細資訊，請參閱 [ExpressRoute 公用文件](../expressroute/index.md)上的 [入門] 索引標籤，並按一下 [最佳化路由]。

| **國家雲端 Azure 區域**| **BGP 社群值** |
| --- | --- |
| **美國政府** |  |
| 美國政府愛荷華州 | 12076:51109 |
| 美國政府維吉尼亞州 | 12076:51105 |

所有 Microsoft 公告的路由會都加上適當的社群值。 

除了上述各項，Microsoft 也將根據其所屬的服務加上標記及前置詞。 這只適用於 Microsoft 對等互連。 下表提供服務與 BGP 社群值的對應。

| **國家雲端中的服務** | **BGP 社群值** |
| --- | --- |
| **美國政府** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| 商務用 Skype Online |12076:5130 |
| CRM Online |12076:5140 |
| 其他 Office 365 Online 服務 |12076:5200 |

> [!NOTE]
> Microsoft 不接受任何您在向 Microsoft 通告的路由上設定的 BGP 社群值。

## <a name="support-for-load-balancer"></a>支援負載平衡器
負載平衡器已在 Azure Government 中正式推出。 如需詳細資訊，請參閱[負載平衡器公開文件](../load-balancer/load-balancer-overview.md)。 

## <a name="support-for-traffic-manger"></a>支援流量管理員
流量管理員已在 Azure Government 中正式推出。 如需詳細資訊，請參閱[流量管理員公開文件](../traffic-manager/traffic-manager-overview.md)。 

## <a name="support-for-vnet-peering"></a>支援 VNet 對等互連 
VNet 對等互連已在 Azure Government 中正式推出。 如需詳細資訊，請參閱 [VNet 對等互連公開文件](../virtual-network/virtual-network-peering-overview.md)。 

## <a name="support-for-vpn-gateway"></a>支援 VPN 閘道 
VPN 閘道已在 Azure Government 中正式推出。 如需詳細資訊，請參閱 [VPN 閘道公開文件](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>




<!--HONumber=Jan17_HO5-->


