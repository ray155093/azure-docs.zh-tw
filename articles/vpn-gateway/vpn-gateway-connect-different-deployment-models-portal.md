---
title: "如何在入口網站中將傳統虛擬網路連線至 Resource Manager 虛擬網路 | Microsoft Docs"
description: "了解如何使用 VPN 閘道和入口網站在傳統 VNet 和 Resource Manager VNet 之間建立 VPN 連線"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: 2c6ffb6c175612c57ddff3a71b2c7a1855c52348


---
# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>在入口網站中從不同的部署模型連接虛擬網路
> [!div class="op_single_selector"]
> * [入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure 目前有兩種管理模型：傳統和 Resource Manager (RM)。 如果您已使用 Azure 一段時間，則可能具有傳統 VNet 上執行的 Azure VM 和執行個體角色。 較新的 VM 和角色執行個體可能會在 Resource Manager 中建立的 VNet 中執行。 本文將逐步引導您將傳統 Vnet 連接到 Resource Manager Vnet，以允許位於不同部署模型中的資源透過閘道連線彼此通訊。

您可以在不同訂用帳戶和不同區域中的 VNet 之間建立連線。 只要設定的閘道是動態或路由式，您也可以連接已連線到內部部署網路的 Vnet。 如需 VNet 對 VNet 連線的詳細資訊，請參閱本文結尾處的 [VNet 對 VNet 常見問題集](#faq) 。

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 對 VNet 連線的部署模型和方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

當此組態有新文章和額外工具可以使用時，我們就會更新下表。 當文章可用時，我們會直接從資料表連結至該文章。<br><br>

**VNet 對 VNet**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet 對等互連**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>開始之前
下列步驟引導您完成為每個 VNet 設定動態或路由式閘道，以及建立閘道之間的 VPN 連線所需的設定。 此組態不支援靜態或路由式閘道。 

在本文中，我們會使用傳統入口網站、Azure 入口網站和 PowerShell。 目前，不可能僅使用 Azure 入口網站建立此組態。

### <a name="prerequisites"></a>必要條件
* 已建立兩個 Vnet。
* Vnet 的位址範圍不會彼此重疊，或與閘道可能連接的任何其他連線範圍重疊。
* 您已安裝最新的 PowerShell Cmdlet (1.0.2 或更新版本)。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。 確定安裝服務管理 (SM) 和 Resource Manager (RM) Cmdlet。 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>設定範例
您可以使用範例設定為參考。

**傳統 VNet 設定**

VNet 名稱 = ClassicVNet  <br>
位置 = West US <br>
虛擬網路位址空間 = 10.0.0.0/24 <br>
子網路 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
區域網路名稱 = RMVNetLocal <br>

**Resource Manager VNet 設定**

VNet 名稱 = RMVNet  <br>
資源群組 = RG1 <br>
虛擬網路的 IP 位址空間 = 192.168.0.0/16 <br>
子網路 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
位置 = 美國東部 <br>
虛擬網路閘道名稱 = RMGateway <br>
閘道公用 IP 名稱 = gwpip <br>
閘道類型 = VPN <br>
VPN 類型 = 路由式 <br>
區域網路閘道 = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>區段 1︰進行傳統 VNet 設定
在此區段中，我們會建立傳統 VNet 的區域網路和閘道。 本區段中的指示會使用傳統入口網站。 目前，Azure 入口網站不提供屬於傳統 VNet 的所有設定。

### <a name="part-1---create-a-new-local-network"></a>第 1 部份 - 建立新的區域網路
開啟 [傳統入口網站](https://manage.windowsazure.com) 並使用您的 Azure 帳戶登入。

1. 在畫面的左下角，按一下 [新增] > [網絡服務] > [虛擬網路] > [加入區域網路]。
2. 在 [指定您的區域網路詳細資料]  視窗中，輸入您要連接的 ARM VNet 名稱。 在 [VPN 裝置 IP 位址 (選擇性)]  中，輸入任何有效的公用 IP 位址。 這只是暫時的預留位置。 我們將在稍後變更此 IP 位址。 按一下視窗右下角的箭號按鈕。
3. 在 [指定位址空間] 頁面的 [起始 IP] 文字方塊中，輸入您要連接之 Resource Manager VNet 的網路首碼和 CIDR 區塊。 此設定用來指定要路由傳送至 RM VNet 的位址空間。

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>第 2 部份 – 建立區域網路與您的 VNet 的關聯
1. 按一下頁面頂端的 [虛擬網路]  以切換至 [虛擬網路] 畫面，然後按一下以選取您的傳統 VNet。 在您的 VNet 頁面上，按一下 [設定]  以瀏覽至組態頁面。
2. 在 [站對站連線能力] 下，選取 [連接到區域網路] 核取方塊。 然後選取您所建立的區域網路。 如果您有多個已建立的區域網路，請務必從下拉式清單中選取您所建立的區域網路來代表 Resource Manager VNet。
3. 按一下頁面底部的 [儲存]  。

### <a name="part-3---create-the-gateway"></a>第 3 部份 – 建立閘道
1. 儲存設定之後，按一下頁面頂端的 [儀表板]  以變更成 [儀表板] 頁面。 按一下 [儀表板] 頁面底部的 [建立閘道]，然後按一下 [動態路由]。 按一下 [是]  即可開始建立閘道。 此組態需要動態路由閘道。
2. 等待閘道建立。 有時可能需要 45 分鐘或更久的時間才能完成。

### <a name="a-nameipapart-4---view-the-gateway-public-ip-address"></a><a name="ip"></a>第 4 部份 - 檢視閘道公用 IP 位址
建立閘道之後，您可以在 [儀表板]  頁面上檢視閘道 IP 位址。 這是您的閘道的公用 IP 位址。 記下或複製公用 IP 位址。 您會在稍後步驟中使用它來建立 Resource Manager VNet 組態的區域網路。

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>區段 2︰進行 Resource Manager VNet 設定
在此區段中，我們會建立 Resource Manager VNet 的虛擬網路閘道和區域網路。 直到您已擷取傳統 VNet 閘道的公用 IP 位址以後，才可開始下列步驟。

已提供螢幕擷取畫面做為範例。 請務必將值取代為您自己的值。 如果您在練習中建立此組態，請參考這些 [值](#values)。

### <a name="part-1---create-a-gateway-subnet"></a>第 1 部份 - 建立閘道子網路
將虛擬網路連接到閘道之前，您必須先建立虛擬網路要連接的閘道子網路。 以 /28 或更高的 CIDR 計數建立閘道子網路 (/27、/26 等)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

透過瀏覽器瀏覽至 [Azure 入口網站](http://portal.azure.com) ，並使用您的 Azure 帳戶登入。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>第 2 部份 - 建立虛擬網路閘道
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="part-3---create-a-local-network-gateway"></a>第 3 部份 - 建立區域網路閘道
「區域網路閘道」通常是指您的內部部署位置。 它會告知 Azure 哪些 IP 位址範圍要路由傳送到此位置，以及該位置的裝置公用 IP 位址。 不過，在此情況下，這是指與您的傳統 VNet 和虛擬網路閘道相關聯的位址範圍和公用 IP 位址。

賦予區域網路閘道一個可供 Azure 參考它的名稱。 建立虛擬網路閘道時，您可以建立區域網路閘道。 在此組態中，您可以使用在 [上一個區段](#ip)中指派給傳統 VNet 閘道的公用 IP 位址。

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

### <a name="part-4---copy-the-public-ip-address"></a>第 4 部份 - 複製公用 IP 位址
一旦完成虛擬網路閘道的建立，複製與閘道相關聯的公用 IP 位址。 您會在進行傳統 VNet 的區域網路設定時用到它。 

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>區段 3︰修改傳統 Vnet 的區域網路
開啟 [傳統入口網站](https://manage.windowsazure.com)。

1. 在傳統入口網站中，在左側向下捲動並按一下 [網路] 。 在 [網路] 頁面上，按一下頁面頂端的 [區域網路]。 
2. 按一下以選取您在第 1 部份中設定的區域網路。 在頁面底部，按一下 [編輯] 。
3. 在 [指定區域網路詳細資料]  頁面上，使用您在上一個區段中為 Resource Manager 閘道建立的公用 IP 位址取代預留位置 IP 位址。 按一下箭號移至下一個區段。 確認 [位址空間]  正確無誤，然後按一下核取記號以接受變更。

## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>區段 4︰建立連線
在此區段中，我們會建立 Vnet 之間的連線。 此區段的步驟需要 PowerShell。 您無法在任何入口網站中建立此連線。 確定您已下載並安裝傳統 (SM) 和 Resource Manager (RM) PowerShell Cmdlet。

1. 在 PowerShell 主控台中登入您的 Azure 帳戶。 下列 Cmdlet 會提示您輸入 Azure 帳戶的登入認證。 登入之後，便會下載您的帳戶設定，以供 Azure PowerShell 使用。
   
        Login-AzureRmAccount 
   
     如果您有多個訂用帳戶，請取得 Azure 訂用帳戶的清單。
   
        Get-AzureRmSubscription
   
    指定您要使用的訂用帳戶。 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
2. 加入您的 Azure 帳戶，才能使用傳統 PowerShell Cmdlet。 若要這麼做，您可使用下列命令：
   
        Add-AzureAccount
3. 執行下列範例來設定共用金鑰。 在此範例中，`-VNetName` 是傳統 VNet 的名稱，而 `-LocalNetworkSiteName` 是在傳統入口網站中設定區域網路時所指定的名稱。 `-SharedKey` 是您可以產生和指定的值。 您在此指定的值，必須與您在下一個步驟中建立連線時指定的值相同。
   
        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
4. 執行下列命令來建立 VPN 連線：
   
    **設定變數**
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
    **建立連線**<br> 請注意，`-ConnectionType` 是 ‘IPsec’，而不是 ‘Vnet2Vnet’。 在此範例中， `-Name` 是您要呼叫您的連線的名稱。 下列範例會建立名為 'rm-to-classic-connection' 的連線。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>確認您的連接
您可以使用傳統入口網站、Azure 入口網站或 PowerShell 來驗證您的連線。 您可以使用下列步驟來驗證您的連線。 使用您自己的值加以取代。

[!INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-faq"></a><a name="faq"></a>VNet 對 VNet 常見問題集
檢視常見問題集詳細資料以取得 VNet 對 VNet 連線的其他資訊。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Dec16_HO1-->


