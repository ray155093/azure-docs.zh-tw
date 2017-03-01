---
title: "Azure 虛擬網路對等互連 - 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站建立虛擬網路對等互連。"
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
translationtype: Human Translation
ms.sourcegitcommit: c3c3938577f610eba522ba593bfab5e93c70e459
ms.openlocfilehash: d47a97fb023e2cc3ad4acaa07eb4edfd1f82509e
ms.lasthandoff: 02/16/2017


---
# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>使用 Azure 入口網站建立虛擬網路對等互連
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

若要使用 Azure 入口網站依據案例建立 VNet 對等互連，請完成下列步驟：

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 若要建立 VNet 對等互連，您必須在兩個 VNet 之間建立兩個連結，每個方向各一。 您可以先建立 VNet1 至 VNet2 的 VNet 對等互連連結。 在 Azure 入口網站中，按一下 [瀏覽] > 選擇 [虛擬網路]

    ![在 Azure 入口網站中建立 VNet 對等互連](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. 在 [虛擬網路] 刀鋒視窗中，選擇 [VNET1]，按一下 [對等互連]，然後按一下 [新增]，如下圖所示：

    ![選擇對等互連](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. 在 [新增對等互連] 刀鋒視窗中，在 [名稱] 中輸入 *LinkToVnet2*，選擇訂用帳戶並在對等 [虛擬網路] 中選擇 [VNET2]，然後按一下 [確定]。

    ![連結至 VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. 建立 VNet 對等互連連結後，您可以看到如下圖所示的連結狀態︰

    ![連結狀態](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. 接下來，建立 VNET2 至 VNET1 的 VNet 對等互連連結。 在 [虛擬網路] 刀鋒視窗中，選擇 [VNET2]，按一下 [對等互連]，然後按一下 [新增]，如下圖所示：

    ![從其他 VNet 對等互連](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. 在 [新增對等互連] 刀鋒視窗中，在 [名稱] 中輸入 *LinkToVnet1*，選擇訂用帳戶，為 [虛擬網路] 選取 [VNET1]，然後按一下 [確定]。

    ![建立虛擬網路磚](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. 建立 VNet 對等互連連結後，您可以看到如下圖所示的連結狀態︰

    ![最終連結狀態](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. 檢查 **LinkToVnet2** 的狀態，它現在也變更為 [已連接]。  
    
    ![最終連結狀態 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [!NOTE]
    > 只有當兩個連結都已連接時，才會建立 VNET 對等互連。
    > 
    > 

每個連結都有幾個可設定的屬性︰

| 選項 | 說明 | 預設值 |
|:--- |:--- |:--- |
| AllowVirtualNetworkAccess |對等 VNet 的位址空間是否包含做為 Virtual_network 標籤內的一部分 |是 |
| AllowForwardedTraffic |要接受或卸除非來自對等互連 VNet 的流量 |否 |
| AllowGatewayTransit |允許對等 VNet 使用您的 VNet 閘道 |否 |
| UseRemoteGateways |使用對等的 VNet 閘道。 必須為對等 VNet 設定閘道，並選取 AllowGatewayTransit。 如果閘道已設定則無法使用此選項。 |否 |

VNet 對等互連中的每個連結都有一組前述屬性。 從入口網站中，您可以按一下 [VNet 對等互連] 連結並變更任何可用選項，按一下 [儲存] 以套用變更。

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 在此範例中，使用者 A 具有訂用帳戶 A 的系統管理權限，而使用者 B 具有訂用帳戶 B 的系統管理權限。
3. 在入口網站中，按一下 [瀏覽]，選擇 [虛擬網路]。 按一下要為其設定對等互連的 VNet。
4. 在您所選的 VNet 的刀鋒視窗中，按一下 [存取控制]，然後按一下 [新增]，如下圖所示：

    ![案例 2 瀏覽](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. 在 [新增存取] 刀鋒視窗上，按一下某個角色並選擇 [網路參與者]，按一下 [新增使用者]，輸入使用者 B 登入名稱並按一下 [確定]。

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

5. 以使用者 B 登入 Azure 入口網站，使用者 B 為訂用帳戶 B 的權限使用者。 請依照前述步驟將使用者 A 新增到「網路參與者」角色，如下圖所示：

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [!NOTE]
    > 您可以在瀏覽器中登出並登入這兩個使用者工作階段以確定授權已成功啟用。
    >
    >

    > [!IMPORTANT]
    > 如果您要在透過 Azure Resource Manager 部署模型建立的兩個 VNet 之間建立對等互連，請繼續進行本節中的其餘步驟。 如果兩個 VNet 是透過不同的部署模型建立的，則略過本節的其餘步驟，並完成此文章中[為透過不同部署模型建立的虛擬網路建立對等互連](#x-model)一節中的步驟。

6. 以使用者 A 登入入口網站，瀏覽至 [VNET3] 刀鋒視窗，按一下 [對等互連]，選取 [我知道我的資源識別碼] 核取方塊，然後以下列範例所示的格式輸入 VNET5 的資源識別碼：
   
    /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/virtualNetworks/{VNETname}
   
    ![資源識別碼](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. 以使用者 B 登入入口網站並依照前述步驟建立從 VNET5 至 VNet3 的對等互連連結。
   
    ![資源識別碼 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. 將會建立對等互連。 連接到 VNet3 的任何 VM 應該能夠與連接到 VNet5 的任何 VM 進行通訊。

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 第一個步驟中，從 HubVnet 至 VNET1 的 VNET 對等互連連結。 請注意，未選取連結的 [允許轉送流量] 選項。
   
    ![基本對等互連](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. 下一個步驟中，可以建立從 VNET1 至 HubVnet 的對等互連連結。 請注意，已選取 [允許轉送流量] 選項。
   
    ![基本對等互連](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. 對等互連建立之後，您可以參考此 [文章](virtual-network-create-udr-arm-ps.md) 並定義使用者定義路由 (UDR)，透過虛擬應用裝置重新導向 VNet1 流量以使用其功能。 當您在路徑中指定下個躍點位址時，可以將其設定為對等 VNet HubVNet 中的虛擬設備 IP 位址

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 如果您要在「相同」訂用帳戶中透過不同部署模型部署的 VNet 之間建立對等互連，請跳至步驟 3。 為「不同」訂用帳戶中透過不同部署模型部署的 VNet 之間建立 VNet 對等互連的功能屬於「預覽」版本。 預覽版本的功能沒有與一般版本功能同樣的可靠性與服務等級協定。 如果您要在不同訂用帳戶中透過不同部署模型部署的 VNet 之間建立對等互連，則必須先完成下列工作：
    - 在 Azure 訂用帳戶中註冊預覽版功能，方法是從 PowerShell 中輸入下列命令︰`Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` 和 `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`。在入口網站中無法完成此步驟。
    - 完成此文章中[跨訂用帳戶的對等互連](#x-sub)一節中的步驟 1 到 6。
3. 若要在此案例中建立 VNET 對等互連，您必須只建立一個連結 (從 Azure Resource Manager 中的虛擬網路至傳統虛擬網路)。 也就是，從 **VNET1** 至 **VNET2**。 在入口網站中，按一下 [瀏覽] > 選擇 [虛擬網路]
4. 在 [虛擬網路] 刀鋒視窗中，選擇 [VNET1] 。 按一下 [對等互連]，然後按一下 [新增]。
5. 在 [新增對等互連] 刀鋒視窗中，為您的連結命名。 在此稱為 [LinkToVNet2] 。 在 [對等詳細資料] 下選取 [傳統] 。
6. 選擇訂用帳戶和對等虛擬網路 [VNET2]。 然後按一下 [確定]。

    ![將 Vnet1 連結至 Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
7. 建立此 VNet 對等互連連結後，兩個虛擬網路會對等互連，您將可看到下列畫面︰

    ![檢查對等互連連線](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## <a name="remove-vnet-peering"></a>移除 VNet 對等互連
1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 移至 [虛擬網路] 刀鋒視窗，按一下 [對等互連]，按一下要移除的連結，然後按一下 [刪除]。

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. 一旦在 VNET 對等互連中移除一個連結，對等連結狀態將會改為已中斷連線。

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. 在此狀態下，您無法重新建立連結直到對等連結狀態變更為初始化為止。 建議您兩個連結都移除後，再重新建立 VNET 對等互連。


