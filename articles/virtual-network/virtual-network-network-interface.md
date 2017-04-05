---
title: "Azure 網路介面 | Microsoft Docs"
description: "了解如何建立和刪除網路介面 (NIC) 以及將公用和私人 IP 位址指派給 NIC。 了解如何從 Azure 虛擬機器連結和卸離 NIC。"
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: f691f3886fce217ea784237f03a4f02ed58e12ee
ms.lasthandoff: 03/28/2017


---

# <a name="network-interfaces"></a>網路介面

了解網路介面 (NIC) 及其使用方式。 NIC 是 Azure 虛擬機器 (VM) 與基礎軟體網路之間互相連線的橋樑。 下圖說明 NIC 提供的功能：

![網路介面](./media/virtual-network-network-interface/nic.png)

本文說明如何運用圖中顯示的概念。 按一下下列任何一個概念，即可直接前往本文的該小節：

- [網路介面](#nics)：NIC 會連線至 Azure 虛擬網路 (VNet) 內的一個子網路。 在上圖中，**VM1** 已連結兩個 NIC，而 **VM2** 已連結一個 NIC。 每個 NIC 會連線至相同的 VNet，但不同的子網路。 本節提供列出現有 NIC 以及建立、變更和刪除 NIC 的步驟。
- [IP 組態](#ip-configs)︰每個 NIC 會有一或多個相關聯的 IP 組態。 每個 IP 組態都有指派的私人 IP 位址。 IP 組態可能有相關聯的公用 IP 位址。 在上圖中，**NIC1** 和 **NIC3** 各有一個相關聯的 IP 組態，而 **NIC2** 有兩個相關聯的 IP 組態。 指派給 NIC1 和 NIC3 的 IP 組態有指派的公用 IP 位址，而指派給 NIC2 的兩個 IP 組態均沒有指派給它的公用 IP 位址。 本節提供建立、變更及刪除以下 IP 組態的步驟：具有使用靜態和動態指派方法指派的私人 IP 位址。 本節也提供建立及解除公用 IP 位址與 IP 組態之關聯的步驟。
- [網路安全性群組](#nsgs)︰網路安全性群組 (NSG) 包含一或多個輸入或輸出安全性規則。 這些規則控制可以傳入和傳出網路介面、子網路或兩者的網路流量類型。 在上圖中，**NIC1** 和 **NIC3** 有相關聯的 NSG，而 **NIC2** 則沒有。 本節提供下列步驟：檢視套用至 NIC 的 NSG、將 NSG 新增至 NIC，以及從 NIC 移除 NSG。
- [虛擬機器](#vms)：VM 至少有一個相連結的 NIC，但可以有數個相連結的 NIC (視 VM 大小而定)。 若要了解每個 VM 大小支援多少個 NIC，請參閱 [Windows](../virtual-machines/virtual-machines-windows-sizes.md) 或 [Linux](../virtual-machines/virtual-machines-linux-sizes.md) VM 大小文章。 本節提供下列步驟：建立單一和多重 NIC 的 VM，以及從現有的 VM 連結和卸離 NIC。

如果您不熟悉 Azure 中的新手 NIC 和 VM，我們建議您先完成[建立第一個 Azure 虛擬網路](virtual-network-get-started-vnet-subnet.md)中的練習，再閱讀本文。 練習可幫助您熟悉 VNet 和 VM。

本文適用於透過 Azure Resource Manager 部署模型部署的 VM 和 NIC。 Microsoft 建議透過 Resource Manager 部署模型 (而非傳統部署模型) 建立資源。 如果您不熟悉這兩種模型之間的差異，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

本文的其餘各節會提供完成所有 NIC 相關工作的步驟。 每一節會列出︰
- 在 Azure 入口網站中完成工作的步驟。 若要完成這些步驟，您必須登入 [Azure 入口網站](http://portal.azure.com)。 如果您沒有帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 使用 Azure PowerShell 搭配命令參考連結來完成工作的命令。 完成[如何安裝及設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)一文中的步驟，以安裝和設定 PowerShell。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 使用 Azure 命令列介面 (CLI) 搭配命令參考連結來完成工作的命令。 完成[如何安裝及設定 Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟，以安裝 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> -h`。

## <a name="nics"></a>網路介面
完成下列各節中建立、檢視、變更或刪除網路介面和設定的步驟︰

### <a name="create-nic"></a>建立網路介面

NIC 可能會連結至 VM，或單獨存在。 若要了解如何將 NIC 連結至 VM，請閱讀本文的[將 NIC 連結至虛擬機器](#vm-attach-nic)一節。

若要建立 NIC，請完成下列步驟：

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在顯示的 [網路介面] 刀鋒視窗中，按一下 [+ 新增]。
4. 在顯示的 [建立網路介面] 刀鋒視窗中，輸入或選取下列設定的值，然後按一下 [建立]：

    |**設定**|**必要？**|**詳細資料**|
    |---|---|---|
    |**名稱**|是|建立 NIC 之後，便無法變更名稱。 名稱必須是您選取的資源群組中唯一的名稱。 請閱讀[命名慣例](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)一文中的命名建議。|
    |**虛擬網路**|是|您只能將 NIC 連接到與 NIC 位於相同訂用帳戶和位置的 VNet。 連結 NIC 的 VM 也必須位於與 NIC 相同的位置和訂用帳戶中。 如果未列出任何 VNet，您需要建立一個。 若要建立 VNet，請完成[虛擬網路](virtual-networks-create-vnet-arm-pportal.md)一文中的步驟。 建立 NIC 後，您就無法變更它所連接的 VNet。|
    |**子網路**|是|選取您所選 VNet 內的子網路。 您可以建立 NIC 之後，變更它連線至的子網路。|
    |**私人 IP 位址指派**|是| 建立 NIC 時，Azure DHCP 伺服器會將私人 IP 位址指派給它。 DHCP 伺服器會從針對 NIC 連線至的子網路所定義的子網路位址範圍中指派可用的位址。 **動態︰**當 NIC 所連結的 VM 處於已停止 (已解除配置) 狀態後啟動時，Azure 可能會指派不同的位址給此 NIC。 如果 VM 未曾處於已停止 (已解除配置) 狀態而重新啟動，位址會維持不變。 **靜態︰**直到您變更靜態位址或刪除 NIC，靜態位址才會變更。 您可以在建立 NIC 之後，變更指派方法。|
    |**網路安全性群組**|否|網路安全性群組可讓您控制進出 NIC 的網路流量。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。 您可以將零個或一個網路安全性群組 (NSG) 套用至 NIC。 也可以將零個或一個 NSG 套用至 NIC 所連線到的子網路。 將 NSG 套用至 NIC 及其連線的子網路時，有時會發生非預期的結果。 若要針對套用至 NIC 的 NSG 進行疑難排解，請參閱[針對 NSG 進行疑難排解](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface)一文。|
    |**訂用帳戶**|是| 連結 NIC 的 VM 與 VM 所連線的 VNet 必須位於相同的訂用帳戶中。|
    |**資源群組**|是| NIC 可以位於與其連結的 VM 或 VM 所連線的 VNet 相同或不同的資源群組中。|
    |**位置**|是|連結 NIC 的 VM 與 VM 所連線的 VNet 必須位於相同的位置。|

Azure 入口網站會建立一個名為 **ipconfig1** 且具有動態私人 IP 位址的主要 IP 組態，並將它與您建立的 NIC 建立關聯。 若要深入了解 IP 組態，請參閱本文的 [IP 組態](#ip-configs)一節。 您無法在建立 NIC 時，指定入口網站所建立之 IP 組態的名稱、指派靜態私人 IP 位址，或指派公用 IP 位址。 如果您使用 PowerShell 或 CLI 建立 NIC，您可以指定 IP 組態的名稱、靜態 IP 位址，以及指派公用 IP 位址。 您可以變更私人 IP 位址指派方法，以及公用 IP 位址是否與 NIC 相關聯 (在 NIC 建立之後)。 若要在 NIC 建立後變更設定，請完成本文的[變更 IP 組態](#change-ip-config)一節中的步驟。

>[!Note]
> Azure 只會在 NIC 連結至 VM 且 VM 第一次啟動之後，將 MAC 位址指派給 NIC。 您無法指定 Azure 指派給 NIC 的 MAC 位址。 在 NIC 遭到刪除或指派給主要 NIC 之主要 IP 組態的私人 IP 位址遭到變更之前，MAC 位址會保持指派給 NIC。 若要深入了解 IP 組態，請參閱本文的 [IP 組態](#ip-configs)一節。

|**工具**|**命令**|
|:---|:---|
|**CLI**|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

### <a name="view-nics"></a>檢視及變更網路介面和設定

若要檢視及變更網路介面和設定，請完成下列步驟︰

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在顯示的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更設定的 NIC。
4. 下列設定會列在針對您所選 NIC 顯示的刀鋒視窗中︰
    - **概觀︰**提供 NIC 的相關資訊，例如指派給它的 IP 位址、NIC 連線至的 VNet/子網路，以及連結 NIC 的 VM (如果它已連結至一部 VM)。下圖顯示名為 **mywebserver256** 之 NIC 的概觀設定：  ![網路介面概觀](./media/virtual-network-network-interface/nic-overview.png)
    - **IP 組態︰**NIC 至少有一個指派的 IP 組態，但是可以有數個 IP 組態指派給它。 若要深入了解 NIC 支援的 IP 組態數目上限，請閱讀 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。 每個 IP 組態都有一個指派的私人 IP 位址，而且可以有一個相關聯的公用 IP 位址。 若要修改所顯示的內容，請完成本文的[將次要 IP 組態新增至 NIC](#create-ip-config)、[變更 IP 組態](#change-ip-config)，或[刪除 IP 組態](#delete-ip-config)各節中的步驟。
    - **DNS 伺服器︰**您可以指定由 Azure DHCP 伺服器指派給 NIC 的 DNS 伺服器。 選擇 Azure 內部 DNS 伺服器或自訂 DNS 伺服器。 若要修改所顯示的內容，請完成本文的[變更 NIC 的 DNS 設定](#dns)一節中的步驟。
    - **網路安全性群組 (NSG)：**顯示 NSG 是否與 NIC 相關聯。 如果 NSG 與 NIC 相關聯，則會顯示相關聯 NSG 的名稱。 若要修改所顯示的內容，請完成本文的[建立及解除 NSG 與網路介面的關聯](#associate-nsg)一節中的步驟。
    - **屬性︰**顯示有關 NIC 的重要設定，以納入其 MAC 位址與其所在的訂用帳戶。 您可以將 NIC 移到不同的資源群組或訂用帳戶，只要您同時移動 NIC 的所有相關資源即可。 如果 NIC 已連結至 VM，例如，您也必須移動 VM 與其相關的任何其他資源。 若要移動 NIC，請閱讀[將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal)一文。 它會列出必要條件，以及如何使用 Azure 入口網站、PowerShell 和 Azure CLI 移動資源。
    - **有效安全性規則︰**如果NIC 已連結至執行中的 VM，而且 NSG 與 NIC、它所連線的子網路或兩者相關聯，則會列出安全性規則。 若要深入了解所顯示的內容，請閱讀[針對網路安全性群組進行疑難排解](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface)一文。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。
    - **有效路由︰**如果 NIC 已連結至執行中的 VM，則會列出路由。 路由是下列各項的組合：Azure 預設路由、任何使用者定義的路由，以及連接 NIC 之子網路可能存在的任何 BGP 路由。 若要深入了解所顯示的內容，請閱讀[針對路由進行疑難排解](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface)一文。 若要深入了解使用者定義的路由，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)文章。
    - **一般 Azure Resource Manager 設定：**若要深入了解一般 Azure Resource Manager 設定，請閱讀[活動記錄](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[存取控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[標籤](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)、[鎖定](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[自動化指令碼](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)文章。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) 用以檢視訂用帳戶中的 NIC；[az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) 用以檢視 NIC 的設定|
|**PowerShell**|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) 用以檢視訂用帳戶中的 NIC 或檢視 NIC 的設定|

### <a name="dns"></a>變更 NIC 的 DNS 設定

若要變更 NIC 的 DNS 設定，請完成下列步驟。 Azure DHCP 伺服器會將 DNS 伺服器指派給 VM。 若要深入了解 NIC 的名稱解析設定，請閱讀 [VM 的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)一文。

1. 針對您要變更設定的 NIC，完成本文的[檢視及變更網路介面和設定](#view-nics)一節中的步驟 1-3。
2. 在您所選 NIC 的刀鋒視窗中，按一下 [DNS 伺服器]。
3. 按一下：
    - **繼承自虛擬網路 (預設值)**︰選擇此選項，可繼承針對 NIC 連線至的虛擬網路所定義的 DNS 伺服器設定。 在 VNet 層級，定義自訂 DNS 伺服器或 Azure 提供的 DNS 伺服器。 Azure 提供的 DNS 伺服器可以針對連線至相同 VNet 的資源 (而非連線至不同 VNet 的資源) 解析名稱。
    - **自訂**︰您可以設定自己的 DNS 伺服器，以解析跨多個 VNet 的名稱。 輸入您要做為 DNS 伺服器之伺服器的 IP 位址。 您指定的 DNS 伺服器位址只能指派給此 NIC，並且會覆寫 NIC 所連線 VNet 的任何 DNS 設定。
4. 按一下 [儲存] 。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="ip-forwarding"></a>變更 NIC 的 IP 轉送

IP 轉送可讓連結 NIC 的 VM：
- 接收以下網路流量：其目的地不是指派給任何 IP 組態 (已指派給 NIC) 的其中一個 IP 位址。
- 傳送以下網路流量：其來源 IP 位址不同於指派給其中一個 IP 組態的 IP 位址。

必須針對 VM 連結的每個 NIC 啟用此設定，而 NIC 會接收 VM 需要轉送的流量。 已連結多個 NIC 或單一 NIC 的 VM 都可以轉送流量。 IP 轉送雖然是 Azure 設定，但 VM 也必須執行能夠轉送流量的應用程式，例如防火牆、WAN 最佳化及負載平衡應用程式。 當 VM 執行網路應用程式時，VM 通常稱為網路虛擬設備 (NVA)。 您可以檢視 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) 中可立即部署的 NVA 清單。 IP 轉送通常使用於使用者定義的路由。 若要深入了解使用者定義的路由，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)文章。

若要變更 NIC 的 IP 轉送設定，請完成下列步驟：

1. 針對您要修改的 NIC，完成本文的[檢視及變更網路介面和設定](#view-nics)一節中的步驟 1-3。
2. 在您所選 NIC 的刀鋒視窗中，按一下 IP 組態。
3. 按一下 [已啟用] 或 [已停用] (預設設定) 來變更設定。
4. 按一下 [儲存] 。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="subnet"></a>變更 NIC 所連線至的子網路

您可以變更 NIC 所連線至的子網路 (但無法變更 VNet)。 若要變更子網路，所有與 NIC 相關聯的 IP 組態都必須被指派動態私人 IP 位址。 若要變更 NIC 所連線至的子網路，請完成下列步驟：

1. 針對您要連線至不同子網路的 NIC，完成本文的[檢視及變更網路介面和設定](#view-nics)一節中的步驟 1-3。
2. 在您所選取 NIC 的刀鋒視窗中，按一下 [IP 組態]。 如有任何所列 IP 組態的任何私人 IP 位址有使用靜態方法指派的私人 IP 位址，您必須完成下列步驟，以將方法變更為動態。 如果使用動態方法指派位址，則繼續執行步驟三︰
    - 針對 IP 組態清單中您要變更的 IP 組態，按一下靜態 IP 位址。
    - 在為此 IP 組態顯示的刀鋒視窗中，按一下 [動態] 做為 [指派] 方法。
    - 按一下 [儲存] 。
3. 從 [子網路] 下拉式清單中選取您要 NIC 連線至的子網路。
4. 按一下 [儲存] 。 新的動態位址是從網路的子網路位址範圍指派而來。 如果您選擇，則可以指派新的子網路位址範圍中的靜態 IP 位址。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="delete-nic"></a>刪除網路介面

只要未連結至 VM，您都可以刪除 NIC。 如果已連結至 VM，您必須先將它從 VM 卸離，才能進行刪除。 若要從 VM 卸離 NIC，請完成本文的[從虛擬機器卸離 NIC](#vm-detach-nic)一節中的步驟。

1. 針對您要刪除的 NIC，完成本文的[檢視及變更網路介面和設定](#view-nics)一節中的步驟 1-2。
2. 以滑鼠右鍵按一下您要刪除的 NIC，然後按一下 [刪除]。
3. 按一下 [是] 以確認刪除 NIC。

當您刪除 NIC 時，會釋出指派給它的所有 MAC 或 IP 位址。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configs"></a>IP 組態
每個 NIC 至少有一個 IP 組態，也稱為**主要**組態。 NIC 也可以有一或多個相關聯的*次要* IP 組態。 您可以指派給 NIC 的 IP 位址數目有所限制。 如需詳細資訊，請閱讀 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。 每個 IP 組態︰
- 都有使用靜態或動態配置方法指派的一個私人 IP 位址。 如果 VM 在處於已停止 (已解除配置) 狀態之後啟動，動態 IP 位址可能變更。 只有在 NIC 遭到刪除時，才會從 NIC 釋出靜態 IP 位址。
- 可以有一個相關聯的公用 IP 位址。

Azure DHCP 伺服器會將 NIC 之主要 IP 組態的私人 IP 位址指派給 VM 作業系統內的 NIC。

將多個 IP 位址指派給 NIC 對有些案例很有幫助，例如︰
- 在單一伺服器上，以不同 IP 位址和 SSL 憑證裝載多個網站或服務。
- 做為網路虛擬設備 (例如防火牆或負載平衡器) 的 VM。
- 能夠將任何 NIC 的任何私人 IP 位址新增到 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。 若要深入了解如何負載平衡多個 IP 設定，請參閱[負載平衡多個 IP 組態](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)文章。

訂用帳戶內可使用的公用 IP 位址數目有限制，而可以指派給 NIC 的私人 IP 位址數目也有限制。 若要深入了解這些限制，請閱讀 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。

### <a name="create-ip-config"></a>將次要 IP 組態新增至 NIC

您可以將所需的多個 IP 組態新增至 NIC，但是在 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文中所列的限制內。 若要將 IP 組態新增至 NIC，請完成下列步驟︰

1. 針對您要新增 IP 組態的 NIC，完成本文的[檢視網路介面設定](#view-nics)一節中的步驟 1-3。
2. 在您所選取 NIC 的刀鋒視窗中，按一下 [IP 組態]。
3. 在針對 IP 組態開啟的刀鋒視窗中按一下 [+ 新增]。
4. 指定下列各項，然後按一下 [確定]，以關閉 [新增 IP 組態] 刀鋒視窗：

    |**設定**|**必要？**|**詳細資料**|
    |---|---|---|
    |**名稱**|是|對 NIC 而言必須是唯一的|
    |**類型**|是|由於您要將 IP 組態新增至現有的 NIC，而且每個 NIC 都必須有一個主要 IP 組態，所以您只能選擇 [次要]。|
    |**私人 IP 位址指派方法**|是|如果 VM 在處於已停止 (已解除配置) 狀態之後重新啟動，**動態**位址可能變更。 直到 NIC 遭到刪除，才會釋出**靜態**位址。 指定子網路位址空間範圍中其他 IP 組態目前未使用的 IP 位址。|
    |**公用 IP 位址**|否|**已停用：**IP 組態目前沒有相關聯的公用 IP 位址資源。 **已啟用：**選取現有的公用 IP 位址，或建立一個新的。 若要了解如何建立公用 IP 位址，請閱讀[公用 IP 位址](virtual-network-public-ip-address.md#create)一文。|
5. 請完成[將多個 IP 位址指派給虛擬機器](virtual-network-multiple-ip-addresses-portal.md#os-config)一文中的指示，以手動方式將次要私人 IP 位址新增至 VM 作業系統。 請勿將任何公用 IP 位址新增至 VM 作業系統。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="change-ip-config"></a>變更 IP 組態

若要變更任何主要或次要 IP 組態的私人和公用 IP 位址設定，請完成下列步驟︰

1. 針對您要修改的 NIC，完成本文的[檢視網路介面設定](#view-nics)一節中的步驟 1-3。
2. 在您所選取 NIC 的刀鋒視窗中，按一下 [IP 組態]。
3. 在針對 IP 組態開啟的刀鋒視窗中，按一下清單中您要修改的 IP 位址。
4. 使用本文的[新增 IP 組態](#create-ip-config)一節中的設定相關資訊，視需要變更設定，然後按一下 [儲存] 以關閉您所選 IP 組態的刀鋒視窗。

>[!NOTE]
>如果主要 NIC 有多個 IP 組態，而且您變更主要 IP 組態的私人 IP 位址，您必須以手動方式將所有次要 IP 位址重新指派給 Windows 內的 NIC (對 Linux 而言並非必要)。 若要以手動方式將 IP 位址指派給作業系統內的 NIC，請閱讀[將多個 IP 位址指派給虛擬機器](virtual-network-multiple-ip-addresses-portal.md#os-config)一文。 請勿將任何公用 IP 位址新增至 VM 作業系統。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="delete-ip-config"></a>刪除 NIC 中的次要 IP 組態

請完成下列步驟來刪除 NIC 中的次要 IP 組態：

1. 針對您要修改的 NIC，完成本文的[檢視網路介面設定](#view-nics)一節中的步驟 1-3。
2. 在您所選取 NIC 的刀鋒視窗中，按一下 [IP 組態]。
3. 以滑鼠右鍵按一下您要刪除的次要 IP 組態，然後按一下 [刪除]。 如果組態有相關聯的公用 IP 位址資源，此資源會與 IP 組態解除關聯，但不會刪除資源。
4. 關閉 [IP 組態] 刀鋒視窗。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="nsgs"></a>網路安全性群組
網路安全性群組 (NSG) 包含一些輸入和輸出規則，可允許或拒絕 NIC 的網路流量。 NIC 和 NIC 所連線至的子網路不需要有相關聯的 NSG。 NSG 可以與 NIC、NIC 所連線至的子網路或兩者產生關聯。 如果 NSG 的規則適用於連接至子網路的所有 NIC，則 NSG 通常會與子網路相關聯。 具有更細微規則的 NSG 則可能會套用至個別的 NIC。 若要深入了解網路安全性群組，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。

### <a name="associate-nsg"></a>建立或解除 NSG 與網路介面的關聯

若要建立或解除 NSG 與 NIC 的關聯，請完成下列步驟︰

1. 針對您要與 NSG 建立或解除關聯的 NIC，完成本文的[檢視及變更網路介面和設定](#view-nics)一節中的步驟 1-3。
2. 在您所選 NIC 的刀鋒視窗中，按一下 [網路安全性群組]。 隨即出現一個頂端包含 [編輯] 的刀鋒視窗。 如果 NIC 目前沒有相關聯的 NSG，則 [網路安全性群組] 會顯示為 [無]。 如果 NIC 目前有相關聯的 NSG，則 [網路安全性群組] 會顯示為 [NSG-Name] (其中 NSG-Name 是目前與 NIC 相關聯的 NSG 名稱)。
3. 按一下 [編輯]。
4. 按一下 [網路安全性群組]。 如果未列出任何網路安全性群組，這是因為您的訂用帳戶沒有任何網路安全性群組。 若要建立 NSG，請完成[網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)一文中的步驟。
5. 在顯示的 [選擇網路安全性群組] 刀鋒視窗中，按一下清單中的現有 NSG 以將建立該 NSG 與 NIC 的關聯，或按一下 [無] 以解除目前 NSG 與 NIC 的關聯。
6. 按一下 [儲存] 。

|**工具**|**命令**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vms"></a>從虛擬機器連結和卸離 NIC

您可以在建立 VM 時將現有的 NIC 連結至該 VM，也可以將現有的 NIC 連結至現有的 VM。 您也可以從至少有兩個 NIC 的現有 VM 卸離一個 NIC。 雖然入口網站會在您建立 VM 時建立 NIC，但不允許您︰

- 建立 VM 時指定要連結的現有 NIC
- 建立已連結多個 NIC 的 VM
- 指定 NIC 的名稱 (入口網站會使用預設名稱建立 NIC)
- 指定私人 IP 位址指派方法為靜態。 入口網站會自動指派動態私人 IP 位址，然而您可以在入口網站建立 NIC 之後變更指派方法。

您可以使用 PowerShell 或 CLI，以無法使用於入口網站的所有先前屬性建立 NIC 或 VM。 在完成下列各節中的工作之前，請考量下列條件約束和行為︰

- 不同的 VM 大小可支援不同數目的 NIC。 若要深入了解每個 VM 大小所支援的 NIC 數目，請參閱 [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小文章。 
- 根據預設，連結至 VM 的第一個 NIC 會定義為「主要」 NIC。 連結至 VM 的所有其他 NIC 則為「次要」 NIC。
- 根據預設，來自 VM 的所有輸出流量會送出指派給主要 NIC 之主要 IP 組態的 IP 位址。 您當然可以控制哪個 IP 位址使用於 VM 作業系統內的輸出流量。
- 在過去，相同可用性設定組中的所有 VM 都必須有單一或多個 NIC。 具有任意多個 NIC 的 VM 現在可存在於相同的可用性設定組中。 然而只有在建立 VM 時，才能將 VM 新增到可用性設定組中。 若要深入了解可用性設定組，請閱讀[在 Azure 中管理 Windows 虛擬機器的可用性](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)一文。
- 雖然連結至相同 VM 的 NIC 可以連線至 VNet 中不同的子網路，但 NIC 必須全部連線至相同的 VNet。
- 您可以將任何主要或次要 NIC 之任何 IP 組態的任何 IP 位址新增至 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。
- 刪除 VM 並不會刪除與其連結的 NIC。 刪除 VM 時，會將 NIC 與該 VM 中斷連結。 您可以將 NIC 連結至其他 VM 或刪除它們。

### <a name="vm-create"></a>在建立虛擬機器時連結一或多個 NIC

您無法將現有的 NIC 連接至新的 VM，或使用 Azure 入口網站建立具有多個 NIC 的 VM。 您可以使用下列 Azure CLI 或 PowerShell 命令，在建立 VM 時連結一或多個現有的 NIC︰

- **CLI：** [az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)
- **PowerShell：** [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-view-nic"></a>檢視連結至虛擬機器的 NIC

1. 使用具備您訂用帳戶之擁有者、參與者或網路參與者角色的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬機器」。 當「虛擬機器」出現於搜尋結果時，按一下它。
3. 在顯示的 [虛擬機器] 刀鋒視窗中，按一下您要檢視已連接網路介面的 VM 名稱。
4. 在針對您所選 VM 顯示的 [虛擬機器] 刀鋒視窗中，按一下 [網路介面]。

|**工具**|**命令**|
|---|---|
|**CLI**|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|**PowerShell**|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="vm-attach-nic"></a>將 NIC 連結至現有的虛擬機器

您要連結 NIC 的 VM 必須支援多個 NIC 並處於已停止 (已解除配置) 狀態。 您無法使用 Azure 入口網站，將 NIC 連結至現有的 VM。 您可以使用下列 Azure CLI 或 PowerShell 命令，將 NIC 連結至 VM︰

- **CLI：** [az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)
- **PowerShell：** [Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-detach-nic"></a>從現有的虛擬機器卸離 NIC

您想要卸離 NIC 的 VM 必須處於已停止 (已解除配置) 狀態，而且目前必須至少連結兩個 NIC。 您可以卸離任何 NIC，但 VM 必須永遠連結至少一個 NIC。 如果卸離主要 NIC，Azure 會將主要屬性指派給已連結至 VM 最久的剩餘 NIC。 您也可以自行指定任何 NIC 做為主要 NIC。 您無法從 VM 卸離 NIC，也無法使用 Azure 入口網站設定 NIC 的主要屬性 (雖然您可以使用 PowerShell 或 CLI 來完成這兩項作業)。 您可以使用下列 Azure CLI 或 PowerShell 命令，從 VM 卸離 NIC︰

- **CLI：** [az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)
- **PowerShell：** [Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>後續步驟
若要使用指令碼建立具有多個 NIC 或 IP 組態的 VM，請閱讀下列文章︰

|**Task**|**工具**|
|---|---|
|**建立具有多個 NIC 的 VM**|[CLI](virtual-network-deploy-multinic-arm-cli.md) 和 [PowerShell](virtual-network-deploy-multinic-arm-ps.md)|
|**建立已指派多個 IP 位址的單一 NIC VM**|[CLI](virtual-network-multiple-ip-addresses-cli.md) 和 [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

