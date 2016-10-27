<properties
   pageTitle="使用 Resource Manager 部署模型和 Azure 入口網站將虛擬網路連結到 ExpressRoute 線路 | Microsoft Azure"
   description="本文件提供如何將虛擬網路 (Vnet) 連結到 ExpressRoute 循環的概觀。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc" />


# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>將虛擬網路連結到 ExpressRoute 電路

> [AZURE.SELECTOR]
- [Azure 入口網站 - Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - 資源管理員](expressroute-howto-linkvnet-arm.md)
- [PowerShell - 傳統](expressroute-howto-linkvnet-classic.md)



本文會協助您使用 Resource Manager 部署模型和 Azure 入口網站將虛擬網路 (VNet) 連結到 Azure ExpressRoute 線路。 虛擬網路可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶。


**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>組態必要條件

- 開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)和[工作流程](expressroute-workflows.md)。
- 您必須擁有作用中的 ExpressRoute 線路。
    - 遵循指示來 [建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md) ，並由您的連線提供者來啟用該線路。

    - 確定您已針對循環設定了 Azure 私用對等。 請參閱 [設定路由](expressroute-howto-routing-portal-resource-manager.md) 一文，以取得路由指示。

    - 請確定已設定 Azure 私用對等，且已開啟您的網路與 Microsoft 之間的 BGP 對等，讓您可以啟用端對端連線。

    - 請確定您有已建立且完整佈建的虛擬網路和虛擬網路閘道。 請遵循指示來建立 [VPN 閘道](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (請只遵循步驟 1 - 5)。

您最多可以將 10 個虛擬網路連結至標準 ExpressRoute 電路。 在使用標準 ExpressRoute 電路時，所有虛擬網路都必須位於相同的地理政治區域內。 如果您已啟用 ExpressRoute 高階附加元件，則可連結 ExpressRoute 電路的地理政治區域以外的虛擬網路，或是將大量的虛擬網路連接到 ExpressRoute 電路。 如需高階附加元件的詳細資訊，請參閱 [常見問題集](expressroute-faqs.md) 。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>將相同訂用帳戶中的虛擬網路連接到線路


### <a name="to-create-a-connection"></a>建立連線

1. 確認正確設定您的 ExpressRoute 電路和 Azure 私人對等互連。 請遵循[建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)和[設定路由](expressroute-howto-routing-arm.md)中的指示。 ExpressRoute 線路看起來應該像下圖。

    ![刪除 ExpressRoute 線路螢幕擷取畫面](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

    >[AZURE.NOTE] 如果您的對等互連是由第 3 層提供者設定，就不會顯示 BGP 組態資訊。 如果線路處於佈建狀態，您應該能夠建立連線。

2. 您現在可以開始佈建將虛擬網路閘道連結至 ExpressRoute 線路的連線。 按一下 [連接] > [新增] 開啟 [新增連線] 刀鋒視窗，然後設定各值。 請參閱下列參考範例。


    ![新增連線螢幕擷取畫面](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


3. 順利設定連線後，您的連線物件就會顯示連接資訊。

    ![連線物件螢幕擷取畫面](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### <a name="to-delete-a-connection"></a>刪除連接

您可以選取連接刀鋒視窗上的 **刪除** 圖示來刪除連接。

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>將不同訂用帳戶中的虛擬網路連接到線路

此時，您無法使用 Azure 入口網站跨訂用帳戶連接虛擬網路。 不過，您可以使用 PowerShell 來執行這項操作。 如需詳細資訊，請參閱 [PowerShell](expressroute-howto-linkvnet-arm.md) 文章。

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。



<!--HONumber=Oct16_HO2-->


