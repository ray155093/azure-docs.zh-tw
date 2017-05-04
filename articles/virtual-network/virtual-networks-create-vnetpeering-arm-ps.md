---
title: "Azure 虛擬網路對等互連 - PowerShell | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 建立虛擬網路對等互連。"
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 57991a59feb784fd2e5277e893110de2a776a060
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>使用 Azure PowerShell 建立虛擬網路對等互連
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

若要使用 PowerShell 建立 VNet 對等互連，請依照下列步驟：

1. 如果您從未用過 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) ，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。

    > [!NOTE]
    > 用於管理 VNet 對等互連的 PowerShell Cmdlet 隨附於 [Azure PowerShell 1.6。](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. 讀取虛擬網路物件：

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. 若要建立 VNet 對等互連，您必須建立兩個連結，每個方向各一。 下列步驟會先建立 VNet1 至 VNet2 的 VNet 對等互連連結︰

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    傳回的輸出︰
        
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
            UseRemoteGateways    : False
            RemoteGateways        : null
            RemoteVirtualNetworkAddressSpace : null

4. 此步驟會建立 VNet2 至 VNet1 的 VNet 對等互連連結：

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    傳回的輸出︰

        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
5. 一旦建立 VNet 對等互連連結之後，請輸入下列命令以檢視連結狀態︰

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    傳回的輸出︰
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    有幾個可設定的 VNet 對等互連屬性︰
   
   | 選項 | 說明 | 預設值 |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |對等 VNet 的位址空間是否包含做為 Virtual_network 標籤內的一部分 |是 |
   | AllowForwardedTraffic |要接受或卸除非來自對等互連 VNet 的流量 |否 |
   | AllowGatewayTransit |允許對等 VNet 使用您的 VNet 閘道 |否 |
   | UseRemoteGateways |使用對等的 VNet 閘道。 對等 VNet 必須設定閘道，並選取 AllowGatewayTransit。 如果閘道已設定則無法使用此選項 |否 |

    VNet 對等互連中的每個連結都有一組前述屬性。 例如，您可以為 VNet1 至 VNet2 的 VNet 對等互連連結將 `AllowVirtualNetworkAccess` 設定為 *True*，並為另一個方向的 VNet 對等互連連結將它設定為 *False*。

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    您可以執行 `Get-AzureRmVirtualNetworkPeering` 以在變更之後再次檢查屬性值。 在執行前述 Cmdlet 之後，您可以在輸出中看到 `AllowForwardedTraffic` 變更為 *True*。

        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null

    對等互連建立之後，VM 應該能夠跨兩個 VNet 與彼此進行通訊。 根據預設，`AllowVirtualNetworkAccess` 為 *True*，且 VNet 對等互連會佈建適當的 ACL 以允許 VNet 之間的通訊。 您仍然可以套用網路安全性群組 (NSG) 規則來封鎖特定子網路或虛擬機器之間的連線，以便以更精細的幅度控制兩個 VNet 之間的存取。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-create-nsg-arm-ps.md)文章。

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

若要使用 PowerShell 建立跨訂用帳戶的 VNet 對等互連，請完成下列步驟︰

1. 以訂用帳戶 A 之權限使用者 A 的帳戶登入 Azure，接著再執行以下 Cmdlet：

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    這不是必要需求。 即使使用者針對其個別 VNet 個別提出對等互連要求，只要要求符合就可以建立對等互連。 將另一個 VNet 的權限使用者新增為本機 VNet 使用者，可以更輕鬆進行設定。
2. 以訂用帳戶 B 之權限使用者 B 的帳戶登入 Azure，接著再執行以下 Cmdlet：

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > 如果您要在透過 Azure Resource Manager 部署模型建立的兩個 VNet 之間建立對等互連，請繼續進行本節中的其餘步驟。 如果兩個 VNet 是透過不同的部署模型建立的，則略過本節的其餘步驟，並完成此文章中[為透過不同部署模型建立的虛擬網路建立對等互連](#x-model)一節中的步驟。

3. 在使用者 A 的登入工作階段中，執行以下命令：

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. 在使用者 B 的登入工作階段中，執行以下 Cmdlet：

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. 建立對等互連後，VNet3 中的任一 VM 應可與 VNet5 中的任一 VM 進行通訊。

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 在此案例中，請執行下列 PowerShell Cmdlet 來建立 VNet 對等互連。 您需要將 `AllowForwardedTraffic` 屬性設定為 *True*，並將 VNET1 連結到 HubVNet，以允許來自對等互連 VNet 位址空間外部的連入流量。

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. 對等互連建立之後，您可以參考此 [文章](virtual-network-create-udr-arm-ps.md) 並定義使用者定義路由 (UDR)，透過虛擬應用裝置重新導向 VNet1 流量以使用其功能。 當您在路由中指定下個躍點位址時，可以將它設定為對等 VNet HubVNet 中的虛擬應用裝置 IP 位址。 以下是範例︰

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. 如果您要在「相同」訂用帳戶中透過不同部署模型部署的 VNet 之間建立對等互連，請跳至步驟 2。 為「不同」訂用帳戶中透過不同部署模型部署的 VNet 之間建立 VNet 對等互連的功能屬於「預覽」版本。 預覽版本的功能沒有與一般版本功能同樣的可靠性與服務等級協定。 如果您要在不同訂用帳戶中透過不同部署模型部署的 VNet 之間建立對等互連，則必須先完成下列工作：
    - 在 Azure 訂用帳戶訂閱中註冊預覽版功能，方法是從 PowerShell 中輸入下列命令︰`Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` 和 `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`
    - 完成此文章中[跨訂用帳戶的對等互連](#x-sub)一節中的步驟 1 到 2。
2. 讀取 **VNET1** 的虛擬網路物件，Azure Resource Manager 虛擬網路，方法是輸入下列命令：

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. 若要在此案例中建立 VNet 對等互連，只需要一個連結，特別是從 **VNET1** 至 **VNET2** 的連結。 此步驟需要知道您的傳統 VNet 的資源識別碼。 資源群組識別碼的格式如下列範例所示︰

           subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    務必以適當的名稱取代 SubscriptionID、ResourceGroupName 和 VirtualNetworkName。

    這可透過輸入下列命令來完成：

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. 建立 VNet 對等互連連結後，您可以看到如以下輸出所示的連結狀態︰
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-a-vnet-peering"></a>移除 VNet 對等互連
1. 若要移除 VNet 對等互連，您需要執行下列 Cmdlet：

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    若要移除這兩個連結，請輸入下列命令︰

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. 一旦在 VNET 對等互連中移除一個連結，對等連結狀態將會變成「已中斷連線」。 在此狀態下，您無法重新建立連結，直到對等連結狀態變更為「初始化」為止。 建議您兩個連結都移除後，再重新建立 VNET 對等互連。


