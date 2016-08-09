<properties
   pageTitle="使用 Powershell Cmdlet 建立 VNet 對等互連 | Microsoft Azure"
   description="了解如何使用 Resource Manager 中的 Azure 入口網站建立虛擬網路。"
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos"/>

# 使用 Powershell Cmdlet 建立 VNet 對等互連

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

若要使用 PowerShell 建立 VNet 對等互連，請依照下列步驟：

1. 如果您從未用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。

注意︰用於管理 VNet 對等互連的 PowerShell Cmdlet 隨附於 [Azure PowerShell 1.6。](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. 讀取虛擬網路物件：

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

	
3. 若要建立 VNet 對等互連，您必須建立兩個連結，每個方向各一。下列步驟會先建立 VNet1 至 VNet2 的 VNet 對等互連連結︰

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.id 

輸出會顯示︰

        Name			: LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic	: False
        AllowGatewayTransit	: False
        UseRemoteGateways	: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

4. 此步驟會建立 VNet2 至 VNet1 的 VNet 對等互連連結

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.id 

輸出會顯示︰

        Name			: LinkToVNet1
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet2
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic	: False
        AllowGatewayTransit	: False
        UseRemoteGateways	: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

5. 一旦建立此 VNET 對等互連連結。您可以看到連結狀態如下︰

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

輸出會顯示︰

        Name			: LinkToVNet2
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null


有幾個可設定的 VNet 對等互連屬性︰

|選項|說明|預設值|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|對等 VNet 的位址空間是否包含做為 Virtual\_network 標籤內的一部分|是|
|AllowForwardedTraffic|允許接受或卸除非來自對等互連 VNet 的流量|否|
|AllowGatewayTransit|允許對等 VNet 使用您的 VNet 閘道|否|
|UseRemoteGateways|使用對等的 VNet 閘道。對等 VNet 必須設定閘道，並選取 AllowGatewayTransit。如果閘道已設定則無法使用此選項|否|

VNet 對等互連中每個連結都具有一組上述的屬性。例如，您可以為 VNet1 至 VNet2 的 VNet 對等互連連結將 AllowVirtualNetworkAccess 設為 True，並為另一個方向的 VNet 對等互連連結將其設為 False。

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

您可以執行 Get-AzureRmVirtualNetworkPeering 以在變更之後再次檢查屬性值。在執行上述 Cmdlet 之後，您可以從輸出中看到 AllowForwardedTraffic 變更為 True。

        Name			: LinkToVNet2
        Id			: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet1
        ProvisioningState	: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic		: True
        AllowGatewayTransit		: False
        UseRemoteGateways		: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

在此案例中建立對等互連之後，您應該可以將這兩個 Vnet 中的任一虛擬機器連接至另一虛擬機器。根據預設，AllowVirtualNetworkAccess 為 True，且 VNet 對等互連會佈建正確的 ACL 以允許 Vnet 之間的通訊。但您仍然可以套用 NSG 規則來封鎖例如特定子網路或虛擬機器之間的連線，以微調控制兩個虛擬網路之間的存取。如需建立 NSG 規則的詳細資訊，請參閱此 [文章](virtual-networks-create-nsg-arm-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

若要透過 PowerShell 建立訂用帳戶之間的 VNet 對等互連，請依照下列步驟︰

1. 以訂用帳戶 A 的權限使用者 A 登入 Azure，執行 Cmdlet：

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

這不是必要需求，即使使用者針對其個別的 Vnet 個別提出對等互連的要求，只要要求符合就可以建立對等互連。將另一個 VNet 的權限使用者新增為本機 VNet 使用者，可以更輕鬆進行設定。

2. 以訂用帳戶 B 的權限使用者 B 登入 Azure，執行 Cmdlet：

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. 然後在使用者 A 的登入工作階段中，執行下列 Cmdlet：

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess


4. 然後在使用者 B 的登入工作階段中，執行下列 Cmdlet：

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. 建立對等互連後，VNet3 中的任一虛擬機器應可與 VNet5 中的任一虛擬機器進行通訊

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 在此案例中，您可以執行以下 PowerShell Cmdlet 來建立 VNet 對等互連，尤其是您需要將 AllowForwardedTraffic 屬性設定為 True，連結 VNET1 至 HubVnet，這允許對等互連 VNet 之外的輸入流量具有位址空間。

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.id

2. 對等互連建立之後，您可以參考此 [文章](virtual-network-create-udr-arm-ps.md) 並定義使用者定義路徑(UDR)，透過虛擬設備將 VNet1 流量重新導向以使用其功能。當您在路徑中指定下個躍點位址時，可以將其設定為對等 VNet HubVNet 中的虛擬設備 IP 位址。範例如下：

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1


## 移除 VNet 對等互連

1.	若要移除 VNet 對等互連，您需要執行 Cmdlet

        Remove-AzureRmVirtualNetworkPeering  
    
    若要移除這兩個連結，如下所示︰

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2 
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2 

3. 一旦在 VNET 對等互連中移除一個連結，對等連結狀態將會改為已中斷連線。
  
4. 在此狀態下，您無法重新建立連結直到對等連結狀態變更為初始化為止。建議您兩個連結都移除後，再重新建立 VNET 對等互連。

<!---HONumber=AcomDC_0803_2016-->