---
title: "Azure Governmenmt 網路 | Microsoft Docs"
description: "這會提供與 Azure Government 之私人連線的功能比較和指引"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/28/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 14aa9126d3426c560c1f9497ed0d7a92448137b4
ms.openlocfilehash: 39e67d449c0fa37b76354dc522216f9473df7b97


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

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>




<!--HONumber=Nov16_HO3-->


