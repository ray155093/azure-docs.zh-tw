---
title: "建立虛擬網路 - Azure 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站建立虛擬網路。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 988510350ceb97a15bb305edff397a0e3212a89d
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>使用 Azure 入口網站建立虛擬網路

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure 有兩個部署模型：Azure Resource Manager 和傳統。 Microsoft 建議透過 Resource Manager 部署模型建立資源。 若要深入了解兩個模型的差異，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。
 
此文章說明如何使用 Azure 入口網站透過 Resource Manager 部署模型建立 VNet。 您也可以使用其他工具透過 Resource Manager 建立 VNet，或從下列清單中選取不同選項以透過傳統部署模型建立 VNet︰

> [!div class="op_single_selector"]
> * [入口網站](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [範本](virtual-networks-create-vnet-arm-template-click.md)
> * [入口網站 (傳統)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (傳統)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI (傳統)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>建立虛擬網路

若要使用 Azure 入口網站建立虛擬網路，請完成下列步驟︰

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 按一下 [新增]  >  [網路]  >  [虛擬網路]，如下圖所示︰

    ![新增虛擬網路](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. 在隨即出現的 [虛擬網路] 刀鋒視窗中，確認已選取 [Resource Manager]，按一下 [建立]，如下圖所示︰

    ![虛擬網路](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. 在隨即出現的 [建立虛擬網路] 刀鋒視窗中，[名稱]輸入 TestVNet，[位址空間] 輸入 192.168.0.0/16，[子網路名稱] 輸入 FrontEnd，[子網路位址範圍] 輸入 192.168.1.0/24，[資源群組] 輸入 TestRG，選取您的 [訂用帳戶]、[位置]，然後按一下 [建立] 按鈕，如下圖所示︰

    ![建立虛擬網路](./media/virtual-network-create-vnet-arm-pportal/3.png)

    或者，您可以選取現有的資源群組。 若要深入了解資源群組，請閱讀 [Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md#resource-groups)。 您也可以選取其他位置。 若要深入了解 Azure 位置和區域，請閱讀[Azure 區域](https://azure.microsoft.com/regions)。

5. 入口網站只能讓您在建立 VNet 時建立一個子網路。 此案例中，必須在建立 VNet 後建立第二個子網路。 若要建立第二個子網路，按一下 [所有資源]，然後按一下 [所有資源] 刀鋒視窗中的 [TestVNet]，如下圖所示︰

    ![已建立的 VNet](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. 在隨即出現的 [TestVNet] 刀鋒視窗中，按一下 [子網路]，然後按一下 [+子網路]，在 [新增子網路] 刀鋒視窗中的 [名稱] 輸入BackEnd、[位址範圍] 輸入 192.168.2.0/24，然後按一下 [確定]，如下圖所示︰

    ![新增子網路](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. 系統會列出兩個子網路，如下圖所示︰
    
    ![VNet 中的子網路清單](./media/virtual-network-create-vnet-arm-pportal/6.png)

這篇文章說明如何建立有兩個子網路的虛擬網路以進行測試。 建立用於實際執行環境的虛擬網路前，建議您先閱讀[虛擬網路概觀](virtual-networks-overview.md)和[虛擬網路規劃和設計](virtual-network-vnet-plan-design-arm.md)，以全面了解虛擬網路以及所有的設定。 

## <a name="next-steps"></a>後續步驟

了解如何連接︰

- 虛擬機器 (VM) 至虛擬網路；請閱讀[建立 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 或[建立 Linux VM](../virtual-machines/linux/quick-create-portal.md)。 但不是如文章中的步驟建立 VNet 和子網路，而是選取現有的 VNet 和子網路來連接 VM。
- 虛擬網路至其他虛擬網路；請閱讀[連接 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。
- 虛擬網路至內部部署網路；使用網站對網站虛擬私人網路 (VPN) 或 ExpressRoute 線路。 如需了解做法，請閱讀[使用網站對網站 VPN 將 VNet 連接到內部部署網路](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)以及[將 VNet 連結至 ExpressRoute 線路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)。
