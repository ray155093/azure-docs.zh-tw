---
title: "建立、變更或刪除 Azure 虛擬網路 | Microsoft Docs"
description: "了解如何建立、變更或刪除虛擬網路。"
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b577891afc52013b712634dd51e7e28efcfc4482
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-networks"></a>建立、變更或刪除虛擬網路

了解如何建立和刪除虛擬網路 (VNet) 以及變更設定，例如現有 VNet 的 DNS 伺服器和 IP 位址空間。 VNet 是您的網路在雲端中的身分。 VNet 是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 對於每個 VNet，您可以︰
- 選擇要指派的位址空間。 位址空間包含一或多個使用 CIDR 表示法所定義的位址範圍，例如 10.0.0.0/16。
- 針對每個 VNet 選擇使用 Azure 所提供的 DNS 伺服器或您自己的 DNS 伺服器。 系統會對連線至 VNet 的所有資源指派此 DNS 伺服器，以解析 VNet 中的名稱。
- 將 VNet 分成多個子網路，讓每個子網路都有自己的位址範圍 (在 VNet 的位址空間內)。 若要了解如何建立、變更和刪除子網路，請閱讀[新增、變更或刪除子網路](virtual-network-manage-subnet.md)一文。

本文會說明如何建立、變更和刪除透過 Azure Resource Manager 部署模型所建立的 VNet。
 
## <a name="before"></a>開始之前

在完成本文任一節的步驟之前，請先完成下列工作︰

- 如果您不熟悉 VNet，建議您先完成[建立第一個 Azure 虛擬網路](virtual-network-get-started-vnet-subnet.md)中的練習，再閱讀本文。 該項練習可幫助您熟悉 VNet。
- 檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，以了解 VNet 的限制。
- 使用 Azure 帳戶來登入 Azure 入口網站、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果您使用 Azure PowerShell 命令來完成本文中的工作，您必須先[安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure PowerShell Cmdlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果您使用 Azure 命令列介面 (CLI) 命令來完成本文中的工作，您必須先[安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。


## <a name="create-vnet"></a>建立虛擬網路

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站中，按一下 [+ 新增]。 在出現的 [新增] 刀鋒視窗中，按一下 [網路]。 在出現的 [網路] 刀鋒視窗中，按一下 [虛擬網路]。
3. 在出現的 [虛擬網路] 刀鋒視窗中，讓 [選取部署模型] 方塊保持選取 [Resource Manager]，然後按一下 [建立]。
4. 在出現的 [建立虛擬網路] 刀鋒視窗中，輸入或選取下列設定的值，然後按一下 [建立]：
    - **名稱**：您選取用來建立 VNet 的[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)的名稱必須是唯一的。 VNet 建立好之後，便無法變更名稱。 您可以隨著時間的推移建立多個 VNet。 請閱讀[命名慣例](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)一文，以獲得能夠讓您更輕鬆地管理多個 VNet 的命名建議。
    - **位址空間**︰以 CIDR 表示法來指定。 您可以定義公用或私人的位址空間 (RFC 1918)。 不論您定義的是公用或私人的位址空間，您都只能從 VNet 內部、互連的 VNet，以及任何已連線到 VNet 的內部部署網路來連線到位址空間。 您無法新增下列位址空間︰
        - 224.0.0.0/4 (多點傳送)
        - 255.255.255.255/32 (廣播)
        - 127.0.0.0/8 (回送)
        - 169.254.0.0/16 (連結-本機)
        - 168.63.129.16/32 (內部 DNS)
    
      雖然在建立 VNet 時只能定義一個位址空間，但您可以在 VNet 建立好之後新增其他位址空間。 若要了解如何進行，請完成本文之[新增或移除位址空間](#add-address-spaces)章節的步驟。

      >[!WARNING]
      >如果 VNet 的位址空間與其他 VNet 或內部部署網路重疊，則彼此將無法連線。 在定義位址空間之前，請考慮 VNet 未來要連線的其他 VNet 或內部部署網路。
      >
      >
    
    - **子網路名稱︰**此名稱必須是 VNet 中的唯一名稱。 子網路建立好之後，便無法變更名稱。 當您在建立 VNet 時，雖然 VNet 不一定要擁有任何子網路，但入口網站仍會要求您定義一個子網路。 當您在建立 VNet 時，入口網站只會允許您定義一個子網路，不過，如果您要在所建立的 VNet 中新增其他子網路，仍可在 VNet 建立好之後進行。 若要在 VNet 中新增子網路，請閱讀[建立、變更或刪除子網路](virtual-network-manage-subnet.md)一文的[建立子網路](virtual-network-manage-subnet.md#create-subnet)章節。 您可以使用 CLI 或 PowerShell 來建立有多個子網路的 VNet。

      >[!TIP]
      >我們常會建立不同的子網路來篩選或控制在子網路之間路由的流量。 在定義子網路之前，請先考慮您想要如何篩選和路由子網路之間的流量。 若要深入了解如何篩選子網路之間的流量，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。 Azure 會自動在子網路之間進行路由，但您可以覆寫 Azure 的預設路由。 若要了解如何操作，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)一文。
      >

    - **子網路位址範圍︰**必須位於您為 VNet 所輸入的**位址空間**內。 您可以指定的最小範圍是 /29，此範圍可提供八個 IP 位址供子網路使用。 為了符合通訊協定的規定，Azure 會保留每個子網路中的第一個和最後一個位址。 Azure 還會保留三個位址供 Azure 服務使用。 因此，使用 /29 子網路位址範圍的 VNet 最終可用的 IP 位址只有三個。 如果您打算將 VNet 連線至 VPN 閘道，就必須建立閘道子網路。 深入了解[閘道子網路位址範圍的具體考量](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)。 若符合特定條件，您可以在子網路建立好之後變更其位址範圍。 若要了解如何變更子網路的位址範圍，請閱讀[新增、變更或刪除子網路](virtual-network-manage-subnet.md)一文的[變更子網路](#change-subnet)章節。
    - **訂用帳戶︰**選取[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 VNet 無法跨越訂用帳戶，但可以使用 Azure VPN 閘道或 VNet 對等互連來連線到其他訂用帳戶的 VNet。 連線到 VNet 的 Azure 資源必須存在於相同訂用帳戶中。
    - **資源群組：**選取現有[資源群組](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)或建立新的資源群組。 連線到 VNet 的 Azure 資源可以存在於相同或不同的資源群組。
    - **位置︰**選取 Azure [位置](https://azure.microsoft.com/regions/) (也稱為區域)。 VNet 無法跨越 Azure 位置，但您可以使用 Azure VPN 閘道將某個位置的 VNet 連線到另一個位置的 VNet。 連線到 VNet 的 Azure 資源必須存在於相同位置中。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>檢視虛擬網路與設定

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要檢視設定的 VNet。
4. 針對您所選 VNet 所出現的刀鋒視窗中會列出下列設定︰
    - **概觀︰**提供 VNet 的相關資訊，例如其位址空間和 DNS 伺服器。 下圖顯示名為 **MyVNet** 之 VNet 的概觀設定：
    
        ![網路介面概觀](./media/virtual-network-manage-network/vnet-overview.png)

      您可以從這個刀鋒視窗將 VNet 移到不同的訂用帳戶或資源群組。 若要了解如何移動 VNet，請閱讀[將資源移動到不同資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 該文會列出必要條件，以及如何使用 Azure 入口網站、PowerShell 和 Azure CLI 來移動資源。 連線到 Vnet 的所有資源也必須隨著 VNet 移動。 
    - **位址空間︰**這裡會列出指派給 VNet 的位址空間。 若要了解如何新增和移除位址空間，請完成本文之[新增或移除位址空間](#address-spaces)章節的步驟。
    - **已連線的裝置︰**這裡會顯示與 VNet 連線的任何資源。 在上圖所示的範例中，有三個網路介面和一個負載平衡器連線到 VNet。 您所建立並連線到 VNet 的任何新資源都會在此列出。 如果您刪除連線到 VNet 的資源，清單中就不會再顯示該資源。
    - **子網路︰**VNet 中現存子網路的清單。 若要了解如何新增和移除子網路，請閱讀[新增、變更或刪除子網路](virtual-network-manage-subnet.md)一文的[新增子網路](virtual-network-manage-subnet.md#create-subnet)和[刪除子網路](virtual-network-manage-subnet.md#delete-subnet)章節。
    - **DNS 伺服器︰**您可以指定要由 Azure 的內部 DNS 伺服器還是自訂的 DNS 伺服器來為連線到 VNet 的裝置提供名稱解析。 在使用 Azure 入口網站來建立 VNet 時，VNet 預設會使用 Azure 的 DNS 伺服器來解析名稱。 若要修改 DNS 伺服器，請完成本文之[新增、變更或移除 DNS 伺服器](#dns-servers)章節的步驟。 
    - **對等互連︰**如果訂用帳戶中已有對等互連，這裡便會列出。 您可以檢視現有對等互連的設定，也可以建立、變更或刪除對等互連。 若要深入了解對等互連，請閱讀[對等互連概觀](virtual-network-peering-overview.md)一文。
    - **屬性︰**顯示 VNet 的相關設定，包括 VNet 的資源識別碼，以及 VNet 所位於的訂用帳戶。
    - **圖表︰**圖表會以視覺方式來呈現所有連線到 VNet 的裝置，並提供裝置的某些重要資訊。 按一下任何裝置，即可透過此檢視來直接管理裝置。
    - **一般 Azure 設定：**若要深入了解一般 Azure 設定，請閱讀[活動記錄](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[存取控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[標籤](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)、[鎖定](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[自動化指令碼](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)文章。

**命令**

|**工具**|**命令**|
|---|---|
|CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/v3.8.0/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="address-spaces"></a>新增或移除位址空間

您可以在 VNet 中新增和移除位址空間。 位址空間必須以 CIDR 表示法來指定，而且同一個 VNet 中的位址空間不能重疊。 您可以定義公用或私人的位址空間 (RFC 1918)。 不論您定義的是公用或私人的位址空間，您都只能從 VNet 內部、互連的 VNet，以及任何已連線到 VNet 的內部部署網路，來連線到 VNet 中的位址空間。 您無法新增下列位址空間︰
    - 224.0.0.0/4 (多點傳送)
    - 255.255.255.255/32 (廣播)
    - 127.0.0.0/8 (回送)
    - 169.254.0.0/16 (連結-本機)
    - 168.63.129.16/32 (內部 DNS)

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要在其中新增或移除位址空間的虛擬網路。
4. 在針對您所選 VNet 所出現的刀鋒視窗中，按一下 [設定] 區段中的 [位址空間]。
5. 在所出現的刀鋒視窗中，對位址空間完成下列其中一個選項︰
    - **新增位址空間︰**在方塊中輸入新的位址空間。 此位址空間不能與 VNet 已定義的現有位址空間重疊。
    - **移除地址空間︰**以滑鼠右鍵按一下位址空間，然後按一下 [移除]。 如果位址空間有現存的子網路，您就無法移除該位址空間。 您必須先刪除位址空間中現存的所有子網路 (以及連線到子網路的任何資源)，才能移除位址空間。
6. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|CLI|僅限 Resource Manager|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>新增、變更或移除 DNS 伺服器

連線到 VNet 的所有 VM 都會向 VNet 的指定 DNS 伺服器註冊，並使用該 DNS 伺服器來解析名稱。 VM 中的每個網路介面 (NIC) 都可以有自己的 DNS 伺服器設定。 如果 NIC 有自己的 DNS 伺服器設定，其設定就會覆寫 VNet 的 DNS 伺服器設定。 若要深入了解 NIC DNS 設定，請閱讀[網路介面的工作和設定](virtual-network-network-interface.md#dns)一文。 若要深入了解 VM 和雲端服務角色執行個體的名稱解析，請閱讀 [VM 與角色執行個體的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)一文。

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。 
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要變更 DNS 設定的虛擬網路。
4. 在針對您所選 VNet 所出現的刀鋒視窗中，按一下 [設定] 區段中的 [DNS 伺服器]。
5. 在所出現的刀鋒視窗中，對 DNS 伺服器選取下列其中一個選項︰
    - **預設值 (由 Azure 提供)︰**所有資源名稱和私人 IP 位址都會自動向 Azure DNS 伺服器進行註冊。 您可以在連線到相同 VNet 的任何資源之間解析名稱。 您無法使用此選項來跨 VNet 解析名稱。 若要跨 VNet 解決名稱，您必須使用自訂的 DNS 伺服器。
    - **自訂︰**您可以為 VNet 新增一或多部伺服器，數量可達 Azure 的限制。 若要深入了解 DNS 伺服器限制，請閱讀 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)一文。 您有下列選擇：
        - **新增地址︰**將伺服器新增至虛擬網路的 DNS 伺服器清單，並向 Azure 註冊 DNS 伺服器。 如果您先前已向 Azure 註冊 DNS 伺服器，您就能從顯示的清單中加以選取。 
        - **移除位址︰**按一下您想要移除之伺服器旁的 **X**。 刪除伺服器只會將它從這個 VNet 清單中移除。 該 DNS 伺服器在 Azure 中仍會保持註冊狀態，以供其他 VNet 使用。 
        - **將 DNS 伺服器位址重新排序**︰請務必要確認，您為環境所列出的 DNS 伺服器順序是正確的。 DNS 伺服器清單不會使用循環配置資源， 而會依其指定的順序來使用。 如果清單上的第一部 DNS 伺服器是可以連線的，用戶端就會使用該 DNS 伺服器，而不管該 DNS 伺服器是否有正常運作。 請移除所有列出的 DNS 伺服器，再以您想要的順序重新新增。
        - **變更位址**：將清單中的 DNS 伺服器醒目提示，然後輸入新名稱。
6. 按一下 [儲存] 。
7. 將連線到 VNet 的 VM 重新啟動，讓這些 VM 獲得新的 DNS 伺服器設定。 這些 VM 會繼續使用其目前的 DNS 設定，直到您將其重新啟動。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>刪除虛擬網路

您只能刪除沒有任何已連線資源的 VNet。 如果 VNet 中的任何子網路有已連線的資源，您必須先刪除 VNet 中所有子網路的已連線資源。 不同資源有不同的刪除指示。 若要了解如何刪除子網路的已連線資源，請閱讀您想要刪除之各個資源類型的適用文件。 若要刪除 VNet，請完成下列步驟：

1. 使用 (至少) 具備您訂用帳戶網路參與者角色權限的帳戶來登入[入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要刪除的 VNet。
4. 在針對您所選 VNet 所出現的刀鋒視窗中，按一下 [設定] 區段中的 [已連線的裝置]，確認 VNet 沒有任何已連線的裝置。 如果有已連線的裝置，您必須先將其刪除，然後才能刪除 VNet。  如果沒有已連線的裝置，按一下刀鋒視窗中的 [概觀]。
5. 按一下刀鋒視窗頂端的 [刪除] 圖示。 
6. 按一下 [是] 以確認要刪除 VNet。


**命令**

|工具|命令|
|---|---|
|CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>接續步驟

- 若要建立 VM 並將它連線到 VNet，請閱讀[建立 VNet 並與 VM 連線](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines)一文。
- 若要篩選 VNet 子網路之間的網路流量，請閱讀[建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)一文。
- 若要讓兩個 VNet 對等互連，請閱讀[建立虛擬網路對等互連](virtual-networks-create-vnetpeering-arm-portal.md#peering-vnets-in-the-same-subscription)一文。
- 若要了解可用來將 VNet 連線到內部部署網路的選項，請閱讀[關於網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams)一文。

