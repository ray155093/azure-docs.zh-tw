---
title: "將傳統虛擬網路連接到 Azure Resource Manager VNet：入口網站 | Microsoft Docs"
description: "了解如何使用 VPN 閘道和入口網站在傳統 VNet 和 Resource Manager VNet 之間建立 VPN 連線"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: c20d72f25571e582310ae65ad1d887dbdb7b011c


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>使用入口網站從不同的部署模型連接虛擬網路
> [!div class="op_single_selector"]
> * [入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure 目前有兩種管理模型：傳統和 Resource Manager (RM)。 如果您已使用 Azure 一段時間，則可能具有傳統 VNet 上執行的 Azure VM 和執行個體角色。 較新的 VM 和角色執行個體可能會在 Resource Manager 中建立的 VNet 中執行。 本文將逐步引導您將傳統 Vnet 連接到 Resource Manager Vnet，以允許位於不同部署模型中的資源透過閘道連線彼此通訊。 

您可以在不同訂用帳戶和不同區域中的 VNet 之間建立連線。 只要設定的閘道是動態或路由式，您也可以連接已連線到內部部署網路的 Vnet。 如需 VNet 對 VNet 連接的詳細資訊，請參閱本文結尾處的 [VNet 對 VNet 的考量](#faq)一節。

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 對 VNet 連線的部署模型和方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

當此組態有新文章和額外工具可以使用時，我們就會更新下表。 當文章可用時，我們會直接從資料表連結至該文章。<br><br>

**VNet 對 VNet**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet 對等互連**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="a-namebeforeabefore-beginning"></a><a name="before"></a>開始之前
下列步驟引導您完成為每個 VNet 設定動態或路由式閘道，以及建立閘道之間的 VPN 連線所需的設定。 此組態不支援靜態或路由式閘道。 

在本文中，我們將使用 Azure 入口網站和 PowerShell。 PowerShell 是建立虛擬網路之間連線的必要項目。 您無法使用入口網站建立此組態的連線。

### <a name="prerequisites"></a>必要條件
* 已建立兩個 Vnet。
* Vnet 的位址範圍不會彼此重疊，或與閘道可能連接的任何其他連線範圍重疊。
* 您已安裝最新的 PowerShell Cmdlet。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。 確定安裝服務管理 (SM) 和 Resource Manager (RM) Cmdlet。 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>設定範例
您可以使用範例設定為參考。

**傳統 VNet 設定**

VNet 名稱 = ClassicVNet  <br>
位址空間 = 10.0.0.0/24 <br>
子網路 1 = 10.0.0.0/27 <br>
資源群組 = ClassicRG <br>
位置 = West US <br>
GatewaySubnet = 10.0.0.32/28 <br>
本機站台 = RMVNetLocal <br>

**Resource Manager VNet 設定**

VNet 名稱 = RMVNet  <br>
位址空間 = 192.168.0.0/16 <br>
子網路 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
資源群組 = RG1 <br>
位置 = 美國東部 <br>
虛擬網路閘道名稱 = RMGateway <br>
閘道類型 = VPN <br>
VPN 類型 = 路由式 <br>
閘道公用 IP 名稱 = gwpip <br>
區域網路閘道 = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>區段 1︰進行傳統 VNet 設定
在本節中，我們將為您的傳統 VNet 建立區域網路 (本機站台) 及虛擬網路閘道。 本節中的指示將使用 Azure 入口網站。 這裡的步驟假設尚未建立傳統 VNet 的 VPN 閘道。 如果您已經有一個閘道，請確認它是動態閘道。 如果是靜態，您必須先刪除該 VPN 閘道，然後繼續進行下列步驟。

### <a name="part-1---configure-the-local-site"></a>第 1 部分 - 設定本機站台

開啟 [Azure 入口網站](https://ms.portal.azure.com)並使用您的 Azure 帳戶登入。

1. 瀏覽至 [所有資源] 並找出傳統虛擬網路。
2. 在 [概觀] 刀鋒視窗上的 [VPN 連線] 區段中，按一下 [閘道] 圖形以建立閘道。

    ![設定 VPN 閘道](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "設定 VPN 閘道")
3. 在 [新增 VPN 連線] 刀鋒視窗中，針對 [連線類型] 選取 [站對站]。
4. 針對 [本機站台]，按一下 [設定必要設定]。 這會開啟 [本機站台] 刀鋒視窗。
5. 在 [本機站台] 刀鋒視窗上，建立您將用來參照 Resource Manager VNet 的名稱。 例如，RMVNetLocal。
6. 如果 Resource Manager VNet 的 VPN 閘道已經有公用 IP 位址，請使用 [VPN 閘道 IP 位址] 欄位的值。 如果您尚未建立 Resource Manager VNet 的虛擬網路閘道，您可以使用預留位置 IP 位址。 請確定預留位置 IP 位址使用有效的格式。 稍後，您必須將預留位置 IP 位址以 Resource Manager 虛擬網路閘道的公用 IP 位址取代。
7. 針對 [用戶端位址空間]，使用 Resource Manager VNet 的虛擬網路 IP 位址空間的值。 此設定用來指定要路由傳送至 RM VNet 的位址空間。
8. 按一下 [確定] 來儲存值，並返回 [新增 VPN 連線] 刀鋒視窗。

### <a name="part-2---create-the-virtual-network-gateway"></a>第 2 部份 - 建立虛擬網路閘道
1. 在 [新增 VPN 連線] 刀鋒視窗上，選取 [立即建立閘道] 核取方塊，並按一下 [選擇性閘道組態] 以開啟 [閘道組態] 刀鋒視窗。 

    ![開啟 [閘道組態] 刀鋒視窗](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "開啟 [閘道組態] 刀鋒視窗")
2. 按一下 [子網路 - 設定必要設定] 以開啟 [新增子網路] 刀鋒視窗。 在此刀鋒視窗中，您會看到 [名稱] 已經使用必要的值 **GatewaySubnet** 進行設定。
3. [位址範圍] 指的是閘道子網路的範圍。 雖然您可以使用 /29 位址範圍 (3 個位址) 建立閘道子網路，建議您建立一個包含更多可用 IP 位址的閘道子網路來容納未來可能需要更多可用 IP 位址的組態。 可能的話，請使用 /27 或 /28。 按一下 [確定] 以建立閘道子網路。
4. 在 [閘道設定] 刀鋒視窗中，[大小] 指的是閘道 SKU。 選取您 VPN 閘道的閘道 SKU。
5. 確認 [路由類型] 是 [動態]，然後按一下 [確定] 以返回 [新增 VPN 連線] 刀鋒視窗。
6. 在 [新增 VPN 連線] 刀鋒視窗上，按一下 [確定] 以開始建立您的 VPN 閘道。 這項作業可能需要 45 分鐘的時間才能完成。


### <a name="a-nameipapart-3---copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>第 3 部分 - 複製虛擬網路閘道公用 IP 位址
建立虛擬網路閘道之後，您可以檢視閘道 IP 位址。 

1. 導覽至您的傳統 VNet，然後按一下 [概觀]。
2. 按一下 [VPN 連線] 以開啟 [VPN 連線] 刀鋒視窗。 在 [VPN 連線] 刀鋒視窗上，您可以檢視公用 IP 位址。 這是指派給虛擬網路閘道的公用 IP 位址。 請記下或複製該 IP 位址。 當您在稍後的步驟中處理 Resource Manager 區域網路閘道組態設定時，便會用到該 IP 位址。 您也可以檢視閘道連線的狀態。 請注意，您所建立的區域網路網站會列為「連線中」。 在您建立連線之後，狀態會變更。 複製閘道 IP 位址之後，請關閉刀鋒視窗。

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>區段 2︰進行 Resource Manager VNet 設定
在此節中，我們會建立 Resource Manager VNet 的虛擬網路閘道和區域網路。 已提供螢幕擷取畫面做為範例。 請務必將值取代為您自己的值。 如果您在練習中建立此組態，請參考這些 [值](#values)。

### <a name="part-1---create-a-gateway-subnet"></a>第 1 部份 - 建立閘道子網路
建立虛擬網路閘道之前，您必須先建立閘道子網路。 以 /28 或更高的 CIDR 計數建立閘道子網路。 (/27、/26 等)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

透過瀏覽器瀏覽至 [Azure 入口網站](http://portal.azure.com) ，並使用您的 Azure 帳戶登入。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>第 2 部份 - 建立虛擬網路閘道
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

建立虛擬網路閘道最多可能需要花費 45 分鐘。 您可以等候 Resource Manager 虛擬網路閘道建立完成，或是繼續進行[第 3 部分 - 建立區域網路閘道](#createlng)。 一旦建立閘道之後，系統會指派一個公用 IP 位址給閘道。 在稍後步驟中，此 IP 位址會用來取代您在第 1 節中所建立之傳統 VNet 本機站台設定的預留位置 IP 位址資訊。 

### <a name="a-namecreatelngapart-3---create-a-local-network-gateway"></a><a name="createlng"></a>第 3 部份 - 建立區域網路閘道

區域網路閘道會指定與您的傳統 VNet 及其虛擬網路閘道相關聯之位址範圍和公用 IP 位址。

- 使用[上一節](#ip)中指派給您傳統 VNet 閘道的公用 IP 位址。 
- 賦予區域網路閘道一個可供 Azure 參考它的名稱。 例如，'ClassicVNetLocal'。
- 使用您指派給您的傳統 VNet 的位址空間 (不只是子網路)。

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>第 3 節：修改傳統 VNet 本機站台設定

在本節中，您將會處理傳統 VNet。 您將會取代您在指定本機站台設定時使用的預留位置 IP 位址。 本節會使用傳統 (SM) PowerShell Cmdlet。 如果尚未執行此動作，請檢視 Resource Manager VNet 的虛擬網路閘道，並複製公用 IP 位址。 這是您將用來取代預留位置 IP 位址的 IP 位址。

確認您已下載這些 Cmdlet 的最新版本，如[開始之前](#before)一節中所指定。

1. 在 Azure 入口網站中，瀏覽至傳統虛擬網路。
2. 在您虛擬網路的刀鋒視窗上，按一下 [概觀]。
3. 在 [VPN 連線] 區段中，按一下圖形中您本機站台的名稱。

    ![VPN-connections](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN 連線")
4. 在 [站對站 VPN 連線] 刀鋒視窗上，按一下站台的名稱。

    ![Site-name](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "本機站台名稱")
5. 在您本機站台的 [連線] 刀鋒視窗上，按一下本機站台的名稱以開啟 [本機站台] 刀鋒視窗。

    ![Open-local-site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "開啟本機站台")
6. 在 [本機站台] 刀鋒視窗上，使用 Resource Manager 閘道的 IP 位址取代 VPN 閘道 IP 位址。

    ![Gateway-ip-address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "閘道 IP 位址")
7. 按一下 [確定] 以更新 IP 位址。


## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>區段 4︰建立連線
在本節中，您將會建立 VNet 之間的連線。 這些步驟需要 PowerShell。 您無法在任何入口網站中建立此連線。 確定您已下載並安裝傳統 (SM) 和 Resource Manager (RM) PowerShell Cmdlet。

### <a name="part-1---log-in-to-your-azure-account"></a>第 1 部分 - 登入您的 Azure 帳戶

1. 以提高的權限開啟 PowerShell 主控台並登入您的 Azure 帳戶。 下列 Cmdlet 會提示您輸入 Azure 帳戶的登入認證。 登入之後，便會下載您的帳戶設定，以供 Azure PowerShell 使用。
   
        Login-AzureRmAccount 
   
2. 如果您有多個訂用帳戶，請取得 Azure 訂用帳戶的清單。
   
        Get-AzureRmSubscription
   
3. 指定您要使用的訂用帳戶。 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. 加入您的 Azure 帳戶，才能使用傳統 PowerShell Cmdlet。 若要這麼做，您可使用下列命令：
   
        Add-AzureAccount

### <a name="part-2---download-your-network-configuration-file"></a>第 2 部份 - 下載您的網路組態檔

因為部署模型中的差異，當在 Azure 入口網站中建立傳統 VNet 設定時，有時候傳統 VNet 和區域網路網站的名稱會變更。 例如，我們使用 Azure 入口網站將傳統 VNet 命名為 'Classic VNet'，並在名稱為 'ClassicRG' 的資源群組中建立它。 網路組態檔中所包含的名稱會轉換為 'Group ClassicRG Classic VNet'。 當指定包含空格的 VNet 名稱時，請使用引號括住值。

1. 執行下列命令以匯出 Azure 網路組態檔。 您可以視需要變更此檔案的位置，以匯出至不同的位置。
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. 使用文字編輯器 (例如 [記事本]) 開啟 .xml 檔，並檢視內容。 確認您的傳統虛擬網路和區域網路站台的名稱值。

### <a name="part-3---set-the-shared-key"></a>第 3 部分 - 設定共用金鑰

設定從傳統 VNet 到 Resource Manager VNet 之連線的共用金鑰。 當使用這些 Cmdlet 時，請務必使用網路組態檔確認 `-VNetName` 和 `-LocalNetworkSiteName` 的值。 當指定包含空格的名稱時，請使用引號括住值。 

- 在此範例中，`-VNetName` 是傳統 VNet 的名稱。 
- `-LocalNetworkSiteName` 是您針對本機站台指定的名稱。
- `-SharedKey` 是您產生和指定的值。 針對此範例，我們使用的是 *abc123*，但是您可以使用更為複雜的值。 重要的是，您在此指定的值必須與您在下一個步驟中建立連線時指定的值相同。

在 PowerShell 主控台中，請執行下列範例來設定您的共用金鑰，確定以您自己的值來取代。 此範例的傳回資料應該顯示狀態 [成功] 。
   
        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
### <a name="part-4---create-the-vpn-connection-by-running-the-following-commands"></a>第 4 部分 - 執行下列命令來建立 VPN 連線：
   
1. 設定變數。
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
2. 建立連線。 在此範例中，`-Name` 是您想用來命名連線的名稱，而非您已建立的項目。 下列範例會建立名稱為 'RM-Classic' 的連線。 請注意，`-ConnectionType` 是 'IPsec'，不是 'Vnet2Vnet'，且 `-SharedKey` 會符合您先前設定的金鑰。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>第 5 節︰驗證您的連線
您可以使用 Azure 入口網站或 PowerShell 來驗證您的連線。 進行驗證時，您可能需要等候一或兩分鐘來建立連線。 連線成功時，連線狀態就會從「連線中」變成「已連線」。

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>驗證從傳統 VNet 到 Resource Manager VNet 的連線

####<a name="verify-your-connection-using-powershell"></a>使用 PowerShell 驗證您的連線


[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>使用 Azure 入口網站驗證您的連線

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>驗證從 Resource Manager VNet 到傳統 VNet 的連線

####<a name="verify-your-connection-using-powershell"></a>使用 PowerShell 驗證您的連線

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>使用 Azure 入口網站驗證您的連線

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>VNet 對 VNet 的考量

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Jan17_HO4-->


