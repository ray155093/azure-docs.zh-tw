<properties 
   pageTitle="使用 PowerShell 將虛擬網路連結到 ExpressRoute 線路 | Microsoft Azure"
   description="本文提供以下內容的概觀：如何使用 Resource Manager 部署模型和 PowerShell 將虛擬網路 (VNet) 連結到 ExpressRoute 線路。"
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="ganesr" />

# 將虛擬網路連結到 ExpressRoute 電路

> [AZURE.SELECTOR]
- [Azure 入口網站 - Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - 資源管理員](expressroute-howto-linkvnet-arm.md)
- [PowerShell - 傳統](expressroute-howto-linkvnet-classic.md)


本文會協助您使用 Resource Manager 部署模型和 PowerShell 將虛擬網路 (VNet) 連結到 Azure ExpressRoute 線路。虛擬網路可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 組態必要條件

- 您需要最新版的 Azure PowerShell 模組 (至少 1.0 版)。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。
- 開始設定之前，請務必檢閱[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)和[工作流程](expressroute-workflows.md)。
- 您必須擁有作用中的 ExpressRoute 線路。
	- 遵循指示來[建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)，並由您的連線提供者來啟用該線路。
	- 確定您已針對循環設定了 Azure 私用對等。請參閱[設定路由](expressroute-howto-routing-arm.md)一文，以取得路由指示。
	- 請確定已設定 Azure 私用對等，且已開啟您的網路與 Microsoft 之間的 BGP 對等，讓您可以啟用端對端連線。
	- 請確定您有已建立且完整佈建的虛擬網路和虛擬網路閘道。請依照指示來建立 [VPN 閘道](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)，但務必要使用 `-GatewayType ExpressRoute`。

您最多可以將 10 個虛擬網路連結至標準 ExpressRoute 電路。在使用標準 ExpressRoute 電路時，所有虛擬網路都必須位於相同的地理政治區域內。

如果您已啟用 ExpressRoute 高階附加元件，則可連結 ExpressRoute 電路的地理政治區域以外的虛擬網路，或是將大量的虛擬網路連接到 ExpressRoute 電路。如需高階附加元件的詳細資訊，請參閱[常見問題集](expressroute-faqs.md)。

## 將相同訂用帳戶中的虛擬網路連接到線路

您可以使用下列 Cmdlet，將虛擬網路閘道連結到 ExpressRoute 線路。執行 Cmdlet 之前，請確定您已建立虛擬網路閘道，並準備好進行連結。

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## 將不同訂用帳戶中的虛擬網路連接到線路

您可以讓多個訂用帳戶共用 ExpressRoute 線路。下圖顯示簡單的圖解，示範多個訂用帳戶共用 ExpressRoute 線路的方式。

大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。組織內的每個部門都可以使用自己的訂用帳戶來部署它們的服務，但可共用單一 ExpressRoute 線路，以連接回內部部署網路。單一部門 (在此範例中：IT) 可以擁有 ExpressRoute 循環。組織內的其他訂用帳戶可以使用 ExpressRoute 電路。

>[AZURE.NOTE] ExpressRoute 循環擁有者需支付專用循環的連線和頻寬費用。所有虛擬網路都會共用相同的頻寬。

![跨訂用帳戶的連線能力](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### 系統管理

「線路擁有者」 是 ExpressRoute 線路資源的已授權進階使用者。電路擁有者能夠建立可由「電路使用者」兌換的授權。「電路使用者」是虛擬網路閘道的擁有者 (它們會與 ExpressRoute 電路位於不同的訂用帳戶內)。「線路使用者」 可以兌換授權 (每個虛擬網路一個授權)。

「電路擁有者」能夠隨時修改及撤銷授權。如果撤銷授權，則在存取權遭撤銷的訂用帳戶中，所有連結連線均會被刪除。

### 循環擁有者作業 

#### 建立授權
	
電路擁有者會建立授權。這樣即會建立授權金鑰，讓電路使用者可用來將其虛擬網路閘道連接到 ExpressRoute 電路。一個授權僅適用於一個連線。

下列 Cmdlet 程式碼片段示範如何建立授權：

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

	$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
		

對此動作的回應將包含授權金鑰和狀態：

	Name                   : MyAuthorization1
	Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
	Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	AuthorizationKey       : ####################################
	AuthorizationUseStatus : Available
	ProvisioningState      : Succeeded

		

#### 檢閱授權

線路擁有者可以藉由執行下列 Cmdlet，來檢閱特定線路上發出的所有授權：

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
	

#### 新增授權

線路擁有者可以使用下列 Cmdlet 來新增授權：

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

	
#### 刪除授權

線路擁有者可以使用下列 Cmdlet 來撤銷/刪除使用者的授權：

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### 循環使用者作業

電路使用者需要具備對等識別碼以及電路擁有者所提供的授權金鑰。授權金鑰是 GUID。

#### 兌換連線授權

線路使用者可以執行下列 Cmdlet 來兌換連結授權：

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### 釋出連線授權

您可以藉由刪除將 ExpressRoute 線路連結到虛擬網路的連線來釋出授權。

## 後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

<!---HONumber=AcomDC_0810_2016------>