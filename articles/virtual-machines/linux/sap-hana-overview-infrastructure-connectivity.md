---
title: "SAP HANA on Azure (大型執行個體) 的基礎結構和連線 | Microsoft Docs"
description: "設定必要的連線基礎結構以使用 SAP HANA on Azure (大型執行個體)。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: a3e99cf86cbe6ae18366b13047d8c2994ff398f4
ms.openlocfilehash: 9adf67b706f866fbb41c2b82970eb0e44aada5c6


---
# <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體) 的基礎結構和連線

在您與 Microsoft 企業帳戶小組之間的 SAP HANA on Azure (大型執行個體) 購買程序完成之後，將需要下列資訊：

- 客戶名稱
- 業務連絡人資訊 (包括電子郵件地址和電話號碼)
- 技術連絡人資訊 (包括電子郵件地址和電話號碼)
- 技術網路連絡人資訊 (包括電子郵件地址和電話號碼)
- Azure 部署區域 (自 2016 年 9 月起為美國西部或美國東部)
- 確認 SAP HANA on Azure (大型執行個體) SKU (組態)
- 針對每個作為部署目的地的 Azure 區域：
  - 用於 P2P 連線的 /29 IP 位址
  - CIDR 區塊 (用於「HANA 大型執行個體」NAT 集區；建議使用 /24)
- 針對每個連接到「HANA 大型執行個體」的 Azure VNet (與Azure 區域無關)：
  - 一或多個 /28 或 /27 IP 位址範圍 (用於客戶 VNet 閘道子網路)
  - 一或多個 CIDR 區塊 (用於客戶 VNet 子網路；建議使用 /24)
- 每個「HANA 大型執行個體」系統的資料：
  - 所需的主機名稱
  - 來自 NAT 集區的所需 IP 位址
- 「SAP HANA on Azure HANA 大型執行個體」將直接連接的 Azure 訂用帳戶的 Azure 訂用帳戶編號
- SAP HANA 執行個體的 SAP HANA SID 名稱 (這是建立必要 SAP HANA 相關磁碟區所需的名稱)

在您提供資訊之後，Microsoft 就會佈建 SAP HANA on Azure (大型執行個體)。

接著，會提供您網路設定資訊來進行下列操作：

- 將您的 Azure VNet 連接到 ExpressRoute 線路，此線路會將 Azure VNet 連接到「HANA 大型執行個體」
  - 針對 Azure Resource Manager：
     - 授權金鑰
     - ExpressRoute PeerID
- 使用已建立的 ExpressRoute 線路和 Azure VNet 來存取「HANA 大型執行個體」

## <a name="creating-an-azure-vnet"></a>建立 Azure VNet

應該使用 Azure Resource Manager 部署模型來建立這個 Azure VNet。 此解決方案不支援舊版 Azure 部署模型 (通常稱為 ASM)。

所建立的 Azure VNet 應該至少有一個租用戶子網路和一個閘道子網路。 應該依指定的方式指派 IP 位址範圍給這些子網路，然後提交給 Microsoft。

> [!IMPORTANT] 
> 只有租用戶和閘道位址區塊應指派給 Azure 訂用帳戶中的 VNet。 P2P 和 NAT 集區位址區塊必須與 VNet 和子網路位址空間分開，因為它們存在於 Azure 訂用帳戶之外。

可以使用多個租用戶子網路 (甚至是使用非連續的位址範圍)，但如先前所述，必須將這些位址範圍事先提交給 Microsoft。

您可以將您喜歡的任何命名標準用於這些租用戶子網路。 不過，**每個 VNet 必須一律有一個且僅有一個閘道子網路**，此子網路會連接到 SAP HANA on Azure (大型執行個體) ExpressRoute 線路，而且**這個閘道子網路必須一律命名為 &quot;GatewaySubnet&quot;**，以確保 ExpressRoute 閘道的位置正確。

> [!WARNING] 
> 將此閘道子網路一律命名為 &quot;GatewaySubnet&quot; 非常重要。

您可以使用 Azure 入口網站、PowerShell、Azure 範本或 Azure CLI 來建立 VNet (請參閱[使用 Azure 入口網站建立虛擬網路](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。

## <a name="creating-a-gateway-subnet"></a>建立閘道子網路

建立 Azure VNet 之後，必須在該 VNet 上建立 ExpressRoute 閘道，以將 VNet 連結到會連接到「大型執行個體」戳記上客戶租用戶的 ExpressRoute 線路。

> [!NOTE] 
> 此步驟最多可能需要 30 分鐘才能完成，因為會在指定的 Azure 訂用帳戶中建立新閘道，然後連接到指定的 Azure VNet。

如果閘道已經存在，請檢查它是否是 ExpressRoute 閘道。 如果不是，就必須刪除該閘道，然後重新建立成 ExpressRoute 閘道。 如果已經建立 ExpressRoute 閘道，則由於 Azure VNet 已經連接到用於內部部署環境連線的 ExpressRoute 線路，因此請繼續進行下面的＜連結 VNet＞小節。

- 使用 (新版) [Azure 入口網站](https://portal.azure.com/) 或 PowerShell 來建立連接到您 VNet 的 ExpressRoute VPN 閘道。
  - 如果您使用 Azure 入口網站，請新增 [虛擬網路閘道]，然後選取 [ExpressRoute] 作為閘道類型。
  - 如果您改為選擇 PowerShell，則請先下載並使用 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)，以確保獲得最佳體驗。 下列命令會建立 ExpressRoute 閘道。 前面有 _$_ 的文字是使用者定義的變數，必須更新成您的特定資訊。

```
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

在此範例中，使用了 HighPerformance 閘道 SKU。 您的選項為 HighPerformance 或 UltraPerformance - SAP HANA on Azure (大型執行個體) 僅支援這些閘道 SKU。

## <a name="linking-vnets"></a>連結 VNet

既然 Azure VNet 已有 ExpressRoute 閘道，您便可以使用 Microsoft 所提供的授權資訊，將 ExpressRoute 閘道連接到為此連線建立的 SAP HANA on Azure (大型執行個體) ExpressRoute 線路。 您只能使用 PowerShell 來執行此操作 (Azure 入口網站目前不支援此操作)。

- 您需針對每個連線使用不同的 AuthGUID 來為每個 VNet 閘道進行下列操作。 下面顯示的前兩個項目來自 Microsoft 所提供的資訊。 此外，AuthGUID 對每個 VNet 及其閘道來說都是特定的。

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

如果您想要將閘道連接到與您訂用帳戶關聯的多個不同 ExpressRoute 線路，您可能需要執行此步驟多次。

## <a name="adding-more-ip-addresses-or-subnets"></a>新增更多 IP 位址或子網路

新增更多 IP 位址或子網路時，您可以使用 Azure 入口網站、PowerShell 或 CLI。

如果您尚未向「SAP HANA on Azure 服務管理」宣告額外的 IP 位址空間範圍，請開啟 Azure 支援要求來新增該範圍。 收到確認之後，請執行後續步驟。

若要從 Azure 入口網站建立額外的子網路，請參閱[使用 Azure 入口網站建立虛擬網路](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文，若要從 PowerShell 建立，則請參閱[使用 PowerShell 建立虛擬網路](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="adding-vnets"></a>新增 VNet

在一開始連接到一或多個 Azure VNet 之後，您可能會想要新增可存取 SAP HANA on Azure (大型執行個體) 的額外 VNet。 首先，請提交 Azure 支援要求，在該要求中同時包含識別特定 Azure 部署的具體資訊，以及額外 Azure VNet 之租用戶子網路和閘道子網路的 IP 位址空間範圍。 接著，「SAP HANA on Azure 服務管理」就會提供您連接額外 VNet 和 ExpressRoute.所需的必要資訊。

新增新 Azure VNet 的步驟：

1. 完成上線程序中的第一個步驟，請參閱上面的＜建立 Azure VNet＞一節。
2. 完成上線程序中的第二個步驟，請參閱上面的＜建立閘道子網路＞一節。
3. 使用新 VNet 的相關資訊來開啟 Azure 支援要求，並要求一個新的「授權金鑰」以將額外的 VNet 連接到「HANA 大型執行個體」ExpressRoute 線路。
4. 收到授權完成的通知之後，請使用 Microsoft 提供的授權資訊來完成上線程序中的第三個步驟，請參閱上面的＜連結 VNet＞一節。

## <a name="increasing-expressroute-circuit-bandwidth"></a>增加 ExpressRoute 線路頻寬

請洽詢「SAP HANA on Azure 服務管理」。 如果系統建議您增加 SAP HANA on Azure (大型執行個體) ExpressRoute 線路的頻寬，請建立 Azure 支援要求。 (針對單一線路頻寬，您最多可以要求增加到 10 Gbps)。作業完成後，您便會收到通知；不需要進行任何額外動作，即可在 Azure 中啟用這個較高的速度。

## <a name="adding-an-additional-expressroute-circuit"></a>新增額外的 ExpressRoute 線路

如果系統告知您需要額外的 ExpressRoute 線路，請洽詢「SAP HANA on Azure 服務管理」，請提出 Azure 支援要求來建立新的 ExpressRoute 線路 (並取得可連線到此線路的授權資訊)。 在提出要求之前，必須先定義將在 VNet 上使用的位址空間，如此「SAP HANA on Azure 服務管理」才能提供授權。

建立新線路且「SAP HANA on Azure 服務管理」設定完成之後，您將會收到通知，當中含有您繼續作業所需的資訊。 請依照上面提供的步驟來建立新 VNet 並將其連接到這個額外的線路。 如果 Azure VNet 已經連接到另一個 ExpressRoute 線路，您就無法將它們連接到這個額外的線路。

## <a name="deleting-a-subnet"></a>刪除子網路

若要移除 VNet 子網路，您可以使用 Azure 入口網站、PowerShell 或 CLI。 如果將位址空間移除，應該通知「SAP HANA on Azure 服務管理」關於此位置空間變更，以便將它從 SAP HANA on Azure (大型執行個體) 可通訊的範圍中移除。

雖然尚未有關於移除子網路的特定、專用 Azure.com 指導方針，但移除子網路的程序就是新增子網路的相反程序。 如需有關建立子網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文。

## <a name="deleting-a-vnet"></a>刪除 VNet

刪除 VNet 時，您可以使用 Azure 入口網站、PowerShell 或 CLI。 「SAP HANA on Azure 服務管理」會移除 SAP HANA on Azure (大型執行個體) ExpressRoute 線路的相關授權，並移除用來與「HANA 大型執行個體」通訊的 IP 位址範圍 (同時包括租用戶和閘道範圍)。

移除 VNet 之後，請開啟 Azure 支援要求來提供要移除的 IP 位址空間範圍。

雖然尚未有關於移除 VNet 的特定、專用 Azure.com 指導方針，但移除 VNet 的程序就是新增 VNet (已在上面敘述) 的相反程序。 如需有關建立 VNet 的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文和[使用 PowerShell 入口網站建立虛擬網路](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文。

為了確保移除所有項目，請刪除下列項目：

- **針對 Azure Resource Manager：**ExpressRoute 連線、VNet 閘道、VNet 閘道公用 IP 及 VNet
- **針對傳統 VM：**VNet 閘道和 VNet

## <a name="deleting-an-expressroute-circuit"></a>刪除 ExpressRoute 線路

若要移除額外的 SAP HANA on Azure (大型執行個體) ExpressRoute 線路，請向「SAP HANA on Azure 服務管理」開啟 Azure 支援要求，然後要求刪除該線路。 您可以視需要在 Azure 訂用帳戶內刪除或保留 VNet。 不過，您必須刪除連線 (如果是 Azure Resource Manager 部署模型)，或將「HANA 大型執行個體」ExpressRoute 線路與連結的 VNet 閘道之間的連線取消連結 (如果是傳統部署模型)。

如果您也想要移除 VNet，請依照上面＜刪除 VNet＞一節中的指導方針操作。





<!--HONumber=Dec16_HO2-->


