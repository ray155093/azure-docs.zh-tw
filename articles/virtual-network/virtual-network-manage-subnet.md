---
title: "建立、變更或刪除 Azure 虛擬網路子網路 | Microsoft Docs"
description: "了解如何建立、變更或刪除虛擬網路子網路。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 38dcdf2f313c236a507e6a418c39812da16c6345
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-network-subnets"></a>建立、變更或刪除虛擬網路子網路

了解如何建立、變更或刪除虛擬網路 (VNet) 子網路。 如果您不熟悉 VNet，建議您先閱讀[虛擬網路概觀](virtual-networks-overview.md)和[建立、變更或刪除虛擬網路](virtual-network-manage-network.md)文章，再建立、變更或刪除子網路。 可以連線到 VNet 的 Azure 資源會連線到 VNet 中的子網路。 VNet 中通常會建立多個子網路，以便︰
- **篩選子網路之間的流量︰**您可以對子網路套用網路安全性群組 (NSG)，以針對連線到 VNet 的所有資源 (例如虛擬機器) 篩選其輸入和輸出網路流量。 若要深入了解如何建立 NSG，請閱讀[建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)一文。
- **控制子網路之間的路由︰**Azure 會建立預設路由，以便在子網路之間自動路由傳送流量。 您可以藉由建立使用者定義的路由 (UDR) 來覆寫預設的 Azure 路由。 若要深入了解 UDR，請閱讀[建立使用者定義的路由](virtual-network-create-udr-arm-ps.md)一文。 

本文會說明如何建立、變更和刪除透過 Azure Resource Manager 部署模型所建立之 VNet 的子網路。
 
## <a name="before"></a>開始之前

在完成本文任一節的步驟之前，請先完成下列工作︰

- 如果您不熟悉 Azure 中的 VNet 和子網路，建議您先完成[建立第一個 Azure 虛擬網路](virtual-network-get-started-vnet-subnet.md)中的練習，再閱讀本文。 該項練習可幫助您熟悉 VNet 和子網路。
- 檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，以了解子網路和 VNet 的限制。
- 使用 Azure 帳戶來登入 Azure 入口網站、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果您使用 Azure PowerShell 命令來完成本文中的工作，您必須先[安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure PowerShell Cmdlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果您使用 Azure 命令列介面 (CLI) 命令來完成本文中的工作，您必須先[安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。


## <a name="create-subnet"></a>建立子網路

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要在其中新增子網路的虛擬網路。
4. 在針對所選虛擬網路所出現的窗格中，按一下 [子網路]。
5. 按一下 [+ 子網路] 。
6. 在 [新增子網路] 刀鋒視窗中，輸入下列參數的值︰
    - **名稱︰**此名稱必須是虛擬網路中的唯一名稱。
    - **位址範圍︰**這必須是 VNet 位址空間內的唯一範圍，而且不能與 VNet 中的其他子網路位址範圍重疊。 此位址空間必須以 CIDR 表示法來指定。 例如，在位址空間為 10.0.0.0/16 的 VNet 中，您可以定義 10.0.0.0/24 的子網路位址空間。 您可以指定的最小範圍是 /29，此範圍可提供八個 IP 位址供子網路使用。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，以 /29 位址範圍所定義的子網路會在子網路中產生三個可用的 IP 位址。 如果您打算將 VNet 連線至 VPN 閘道，就必須建立閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)。 若符合特定條件，您可以在子網路建立好之後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請閱讀本文的[變更子網路](#change-subnet)一節。
    - **網路安全性群組 (NSG)：**您可以選擇性地建立現有 NSG 與子網路的關聯，以控制子網路的輸入和輸出網路流量篩選。 NSG 必須和 VNet 存在於相同的訂用帳戶和位置，並透過 Resource Manager 部署模型來建立。 若要深入了解如何建立 NSG，請閱讀[網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)一文。
    - **路由表︰**您可以選擇性地建立現有路由表和子網路的關聯，以控制路由至其他網路的網路流量。 路由表必須和 VNet 存在於相同的訂用帳戶和位置，並透過 Resource Manager 部署模型來建立。 若要深入了解如何建立路由表，請閱讀[使用者定義的路由](virtual-network-create-udr-arm-ps.md)文章。
    - **使用者**︰您可以使用內建角色或自有的自訂角色來控制子網路的存取。 若要深入了解如何指派角色和使用者以存取子網路，請閱讀[使用角色指派來管理 Azure 資源的存取權](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access)一文。
7. 按一下 [確定] 按鈕以在所選 VNet 中新增子網路。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>變更子網路設定

您可以對連線到子網路的資源變更 NSG、路由表和使用者的子網路存取權。 若要了解這些設定，請閱讀本文之[建立子網路](#create-subnet)一節的步驟 6。 若要變更子網路的位址空間，則不能有任何資源連線到子網路。 如果有資源連線到子網路，您必須先刪除連線到子網路的資源，才能變更位址範圍。 不同資源有不同的刪除指示。 若要了解如何刪除子網路的已連線資源，請閱讀您想要刪除之各個資源類型的適用文件。 完成下列步驟來變更子網路的位址範圍︰

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要變更子網路位址範圍的 VNet。
4. 按一下您想要變更位址範圍的子網路。
5. 在針對所選子網路所出現的刀鋒視窗中，於 [位址範圍] 方塊中輸入新的位址範圍。 這必須是 VNet 位址空間內的唯一範圍，而且不能與 VNet 中的其他子網路位址範圍重疊。 此位址空間必須以 CIDR 表示法來指定。 例如，在位址空間為 10.0.0.0/16 的 VNet 中，您可以定義 10.0.0.0/24 的子網路位址空間。 您可以指定的最小範圍是 /29，此範圍可提供八個 IP 位址供子網路使用。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，使用 /29 位址範圍的子網路最終可用的 IP 位址只有三個。 如果您打算將 VNet 連線至 VPN 閘道，就必須建立閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)。 若符合特定條件，您可以在子網路建立好之後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請閱讀本文的[變更子網路](#change-subnet)一節。
6. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>刪除子網路

您只能刪除沒有任何已連線資源的子網路。 如果有資源連線到子網路，您必須先刪除連線到子網路的資源。 不同資源有不同的刪除指示。 若要了解如何刪除子網路的已連線資源，請閱讀您想要刪除之各個資源類型的適用文件。

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要從中刪除子網路的 VNet。
4. 在針對您所選 VNet 所出現的刀鋒視窗中，按一下 [設定] 底下的 [子網路]。
5. 在 [子網路] 刀鋒視窗中所出現的子網路清單內，以滑鼠右鍵按一下您想要刪除的子網路，按一下 [刪除]，然後按一下 [是] 來刪除子網路。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>接續步驟

若要建立 VM 並將它連線到子網路，請閱讀[建立 VNet 並與 VM 連線](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines)一文。
