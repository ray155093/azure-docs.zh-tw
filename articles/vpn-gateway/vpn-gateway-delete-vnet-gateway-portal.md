---
title: "刪除虛擬網路閘道：Azure 入口網站：Resource Manager | Microsoft Docs"
description: "在 Resource Manager 部署模型中使用 PowerShell 刪除虛擬網路閘道。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b4949db62ccd31cf6ce3d19df5459367cac99b59
ms.lasthandoff: 03/31/2017


---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>使用入口網站刪除虛擬網路閘道
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [傳統 - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

如果您想要刪除 VPN 閘道組態的虛擬網路閘道，您可以採取幾種不同的方法。

- 如果您想要刪除所有內容並重頭開始 (例如使用測試環境的情況)，您可以刪除資源群組。 刪除資源群組時，會一併刪除群組內的所有資源。 只有在您不想保留資源群組中的任何資源時，才建議使用此方法。 您無法使用這種方法，選擇性地只刪除一些資源。

- 如果您想要保留資源群組中的部分資源，刪除虛擬網路閘道會變得稍微複雜一點。 您必須先刪除任何相依於閘道的資源，才可以刪除虛擬網路閘道。 您遵循的步驟取決於您所建立的連線類型以及每個連線的相依資源。

##<a name="deletegw"></a>刪除 VPN 閘道

若要刪除虛擬網路閘道，您必須先刪除虛擬網路閘道的每個相關資源。 由於相依性，您必須依照特定順序刪除資源。

###<a name="step-1-navigate-to-the-virtual-network-gateway"></a>步驟 1：瀏覽至虛擬網路閘道

在 [Azure 入口網站](https://portal.azure.com)的 [所有資源] 中，按一下您想要刪除的虛擬網路閘道。 閘道的圖形是︰

![尋找虛擬網路閘道](./media/vpn-gateway-delete-vnet-gateway-portal/gw.png)

###<a name="step-2-delete-connections"></a>步驟 2︰刪除連線

1. 在虛擬網路閘道的刀鋒視窗中，按一下 [連線] 以檢視與閘道的所有連線。
2. 按一下連線名稱資料列上的 [...]，然後從下拉式清單選取 [刪除]。
3. 按一下 [是] 確認要刪除連線。 如果您有多個連線，請刪除每個連線。

###<a name="step-3-delete-the-local-network-gateways"></a>步驟 3：刪除區域網路閘道
1. 在 [所有資源] 中，找出與每個連線相關聯的區域網路閘道。 區域網路閘道的圖形是︰

    ![尋找區域網路閘道](./media/vpn-gateway-delete-vnet-gateway-portal/lng.png)
2. 在區域網路閘道的 [概觀] 刀鋒視窗中，按一下 [刪除]。

###<a name="step-4-delete-the-virtual-network-gateway"></a>步驟 4：刪除虛擬網路閘道
1. 在 [所有資源] 中，找出您想要刪除的虛擬網路閘道。
2. 在 [概觀] 刀鋒視窗中，按一下 [刪除] 以刪除閘道。

>[!NOTE]
> 如果除了 S2S 組態，您還擁有此 VNet 適用的 P2S 組態，刪除虛擬網路閘道將自動中斷連接所有的 P2S 用戶端，而不會發出任何警告。
>
>

###<a name="step-5-delete-the-public-ip-address-for-the-gateway"></a>步驟 5：刪除閘道的公用 IP 位址

1. 在 [所有資源] 中，找出已指派給閘道的公用 IP 位址。 如果虛擬網路閘道為主動-主動，您會看到兩個公用 IP 位址。 公用 IP 位址的圖形是︰

    ![公用 IP 位址](./media/vpn-gateway-delete-vnet-gateway-portal/pip.png)

2. 在公用 IP 位址的 [概觀] 頁面上，按一下 [刪除]，然後按一下 [是] 以確認。

###<a name="step-6-delete-the-gateway-subnet"></a>步驟 6：刪除閘道子網路

1. 在 [所有資源] 中，找出虛擬網路。 
2. 在 [子網路] 刀鋒視窗中，按一下 [GatewaySubnet]，然後按一下 [刪除]。 
3. 按一下 [是] 確認要刪除閘道子網路。

##<a name="deleterg"></a>藉由刪除資源群組來刪除 VPN 閘道

如果您不在乎保留資源群組中任何資源，而只想要從頭開始，您可以刪除整個資源群組。 這是移除所有項目的快速方法。 下列步驟僅適用於 Resource Manager 部署模型。

1. 在 [所有資源] 中，找出資源群組，然後按一下以開啟刀鋒視窗。
2. 按一下 [刪除] 。 在 [刪除] 刀鋒視窗中，檢視受影響的資源。 請確定您想要刪除所有資源。 如果不是，請使用這篇文章上方之[刪除 VPN 閘道](#deletegw)中的步驟。
3. 若要繼續，請輸入您想要刪除之資源群組的名稱，然後按一下 [刪除]。
