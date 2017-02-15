---
title: "如何使用 Azure 入口網站設定 ExpressRoute 線路的路由 | Microsoft Docs"
description: "本文將逐步引導您為 ExpressRoute 線路建立和佈建私用、公用及 Microsoft 對等。 本文也示範如何檢查狀態、更新或刪除線路的對等。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: ec5e547b88bedd50f451997616c7d72b0b1b4bd4
ms.openlocfilehash: 879207512b7c61d48dcd25342ab88121258ba853


---
# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>建立和修改 ExpressRoute 電路的路由
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [傳統 - PowerShell](expressroute-howto-routing-classic.md)
> * [視訊 - 私用對等互連](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [視訊 - 公用對等互連](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [視訊 - Microsoft 對等互連](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

本文將逐步引導您使用 Azure 入口網站和資源管理員部署模型，以建立和管理 ExpressRoute 線路的路由組態。

**關於 Azure 部署模型**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>組態必要條件
* 開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)頁面、[路由需求](expressroute-routing.md)頁面和[工作流程](expressroute-workflows.md)頁面。
* 您必須擁有作用中的 ExpressRoute 線路。 繼續之前，請遵循指示來 [建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md) ，並由您的連線提供者來啟用該線路。 ExpressRoute 線路必須處於已佈建和已啟用狀態，您才能執行如下所述的 Cmdlet。

這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的線路。 如果您使用的服務提供者是提供受管理的第 3 層服務 (通常是 IPVPN，如 MPLS)，您的連線提供者會為您設定和管理路由。 

> [!IMPORTANT]
> 我們目前不會透過服務管理入口網站來公告服務提供者所設定的對等。 我們正努力在近期推出這項功能。 請在設定 BGP 對等之前，洽詢您的服務提供者。
> 
> 

您可以為 ExpressRoute 線路設定一個、兩個或全部三個對等 (Azure 私用、Azure 公用和 Microsoft)。 您可以依自己選擇的任何順序設定對等。 不過，您必須確定一次只完成一個對等的設定。 

## <a name="azure-private-peering"></a>Azure 私用對等
本節提供如何為 ExpressRoute 線路建立、取得、更新和刪除 Azure 私用對等組態的指示。 

### <a name="to-create-azure-private-peering"></a>建立 Azure 私用對等
1. 設定 ExpressRoute 電路。 先確定連線提供者已完整佈建電路，再繼續作業。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 設定線路的 Azure 私用對等。 繼續執行接下來的步驟之前，請確定您有下列項目：
   
   * 主要連結的 /30 子網路。 這不能在保留給虛擬網路的任何位址空間中。
   * 次要連結的 /30 子網路。 這不能在保留給虛擬網路的任何位址空間中。
   * 供建立此對等的有效 VLAN ID。 請確定線路有沒有其他對等使用相同的 VLAN ID。
   * 對等的 AS 編號。 您可以使用 2 位元組和 4 位元組 AS 編號。 您可以將私用 AS 編號用於此對等。 請確定您不是使用 65515。
   * MD5 雜湊 (如果選擇使用)。 **這是選擇性的**。
3. 選取 Azure 私用對等資料列，如下所示。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. 設定私用對等。 下圖顯示組態範例。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. 指定所有參數後，請儲存組態。 成功接受組態後，您會看到類似以下範例的畫面。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-view-azure-private-peering-details"></a>檢視 Azure 私用對等詳細資訊
選取 Azure 私用對等，即可檢視該對等的屬性。

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-update-azure-private-peering-configuration"></a>更新 Azure 私用對等組態
您可以選取對等的資料列並修改對等屬性。 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="to-delete-azure-private-peering"></a>刪除 Azure 私用對等
選取如下所示的刪除圖示，即可移除對等組態。

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="azure-public-peering"></a>Azure 公用對等
本節提供如何為 ExpressRoute 線路建立、取得、更新和刪除 Azure 公用對等組態的指示。 

### <a name="to-create-azure-public-peering"></a>建立 Azure 公用對等
1. 設定 ExpressRoute 電路。 先確定連線提供者已完整佈建電路，再繼續作業。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 設定線路的 Azure 公用對等。 繼續執行接下來的步驟之前，請確定您有下列項目：
   
   * 主要連結的 /30 子網路。 
   * 次要連結的 /30 子網路。 
   * 用來設定此對等的所有 IP 位址都必須是有效的公用 IPv4 位址。
   * 供建立此對等的有效 VLAN ID。 請確定線路有沒有其他對等使用相同的 VLAN ID。
   * 對等的 AS 編號。 您可以使用 2 位元組和 4 位元組 AS 編號。
   * MD5 雜湊 (如果選擇使用)。 **這是選擇性的**。
3. 選取 Azure 公用對等資料列，如下所示。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. 設定公用對等。 下圖顯示組態範例。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. 指定所有參數後，請儲存組態。 成功接受組態後，您會看到類似以下範例的畫面。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-view-azure-public-peering-details"></a>檢視 Azure 公用對等詳細資訊
選取 Azure 公用對等，即可檢視該對等的屬性。

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-update-azure-public-peering-configuration"></a>更新 Azure 公用對等組態
您可以選取對等的資料列並修改對等屬性。 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="to-delete-azure-public-peering"></a>刪除 Azure 公用對等
選取如下所示的刪除圖示，即可移除對等組態。

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="microsoft-peering"></a>Microsoft 對等互連
本節提供如何為 ExpressRoute 線路建立、取得、更新和刪除 Microsoft 對等組態的指示。 

### <a name="to-create-microsoft-peering"></a>建立 Microsoft 對等
1. 設定 ExpressRoute 電路。 先確定連線提供者已完整佈建電路，再繼續作業。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 設定線路的 Microsoft 對等。 繼續之前，請確定您擁有下列資訊。
   
   * 主要連結的 /30 子網路。 這必須是您所擁有且註冊在 RIR / IRR 中的有效公用 IPv4 首碼。
   * 次要連結的 /30 子網路。 這必須是您所擁有且註冊在 RIR / IRR 中的有效公用 IPv4 首碼。
   * 供建立此對等的有效 VLAN ID。 請確定線路有沒有其他對等使用相同的 VLAN ID。
   * 對等的 AS 編號。 您可以使用 2 位元組和 4 位元組 AS 編號。
   * **公告的首碼：** 您必須提供一份您打算在 BGP 工作階段上公告的所有首碼的清單。 只接受公用 IP 位址首碼。 如果您打算傳送一組首碼，您可以傳送逗號分隔清單。 這些首碼必須在 RIR / IRR 中註冊給您。
   * **客戶 ASN：** 如果您要公告的首碼未註冊給對等 AS 編號，您可以指定它們所註冊的 AS 編號。 **這是選擇性的**。
   * **路由登錄名稱：** 您可以指定可供註冊 AS 編號和首碼的 RIR / IRR。 **這是選擇性。**
   * MD5 雜湊 (如果選擇使用)。 **這是選擇性。**
3. 您可以選取您要設定的對等，如下所示。 選取 Microsoft 對等資料列。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. 設定 Microsoft 對等。 下圖顯示組態範例。
   
   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. 指定所有參數後，請儲存組態。 
   
    如果您的電路取得所需的驗證狀態 (如下所示)，您必須開啟支援票證，向我們的支援小組出示首碼擁有權的證明。    
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

    您可以直接從入口網站開啟支援票證，如下所示     

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. 成功接受組態後，您會看到類似以下範例的畫面。
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-view-microsoft-peering-details"></a>檢視 Microsoft 對等詳細資料
選取 Azure 公用對等，即可檢視該對等的屬性。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="to-update-microsoft-peering-configuration"></a>更新 Microsoft 對等組態
您可以選取對等的資料列並修改對等屬性。 

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-delete-microsoft-peering"></a>刪除 Microsoft 對等
選取如下所示的刪除圖示，即可移除對等組態。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="next-steps"></a>後續步驟
下一步， [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)。

* 如需 ExpressRoute 工作流程的詳細資訊，請參閱 [ExpressRoute 工作流程](expressroute-workflows.md)。
* 如需線路對等的詳細資訊，請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)。
* 如需使用虛擬網路的詳細資訊，請參閱 [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。




<!--HONumber=Dec16_HO2-->


