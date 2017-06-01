---
title: "建立、變更或刪除 Azure 網路介面 | Microsoft Docs"
description: "了解何謂網路介面 (NIC)，以及要如何建立網路介面、變更其設定和加以刪除。"
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
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: f1fb0f6348b579121be64bff4411952026f8528d
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017


---

# <a name="create-change-or-delete-network-interfaces"></a>建立、變更或刪除網路介面

了解如何建立網路介面 (NIC)、變更其設定和加以刪除。 NIC 可讓 Azure 虛擬機器 (VM) 與網際網路、Azure 以及內部部署資源進行通訊。 在使用 Azure 入口網站建立 VM 時，入口網站會以預設設定為您建立一個 NIC。 您也可改為選擇以自訂設定建立 NIC，並在加以建立時對其新增一或多個 VM。 您也可以變更現有 NIC 的預設 NIC 設定。 本文說明如何以自訂設定建立 NIC、變更現有的 NIC 設定 (例如，網路篩選指派 (網路安全性群組)、子網路指派、DNS 伺服器設定和 IP 轉送)，以及刪除 NIC。 

如果您需要新增、變更或移除 NIC 的 IP 位址，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。 如果您需要在 VM 中新增或移除 NIC，請閱讀[新增或移除 NIC](virtual-network-network-interface-vm.md) 一文。 


## <a name="before"></a>開始之前

在完成本文任一節的任何步驟之前，請先完成下列工作︰

- 檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，以了解 NIC 的限制。
- 使用 Azure 帳戶來登入 Azure 入口網站、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果要使用 PowerShell 命令執行本文中的工作，請完成[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟來安裝和設定 Azure PowerShell。 請確定您已安裝最新版的 Azure PowerShell commandlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果要使用 PowerShell 命令列介面 (CLI) 執行本文中的工作，請完成[如何安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟來安裝和設定 Azure CLI。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。

## <a name="create-nic"></a>建立 NIC
在使用 Azure 入口網站建立 VM 時，入口網站會以預設設定為您建立一個 NIC。 如果您想自行指定所有 NIC 設定，您可以使用自訂設定來建立 NIC，並在建立 VM 時將 NIC 連結至 VM。 您也可以建立 NIC 並將它新增至現有 VM。 若要了解如何使用現有 NIC 來建立 VM，或如何在現有 VM 中新增或移除 NIC，請閱讀[新增或移除 NIC](virtual-network-network-interface-vm.md) 一文。 在建立 NIC 之前，用來建立 NIC 的相同位置和訂用帳戶中必須已有既存的虛擬網路 (VNet)。 若要了解如何建立 VNet，請閱讀[建立 VNet](virtual-networks-create-vnet-arm-pportal.md) 一文。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在顯示的 [網路介面] 刀鋒視窗中，按一下 [+ 新增]。
4. 在顯示的 [建立網路介面] 刀鋒視窗中，輸入或選取下列設定的值，然後按一下 [建立]：

    |設定|必要？|詳細資料|
    |---|---|---|
    |名稱|是|名稱必須是您選取的資源群組中唯一的名稱。 在經過一段時間後，您的 Azure 訂用帳戶中可能會有好幾個 NIC。 請閱讀[命名慣例](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)一文以了解有關建立命名慣例時的建議，以便您可以更輕鬆地管理好幾個 NIC。 建立 NIC 之後，便無法變更名稱。|
    |虛擬網路|是|選取 NIC 所要連線的 VNet。 您只能將 NIC 連接到與 NIC 位於相同訂用帳戶和位置的 VNet。 建立 NIC 後，您就無法變更它所連接的 VNet。 用來新增 NIC 的 VM 也必須位於與 NIC 相同的位置和訂用帳戶中。|
    |子網路|是|選取您所選 VNet 內的子網路。 您可以建立 NIC 之後，變更它連線至的子網路。|
    |私人 IP 位址指派|是| 選擇下列任一指派方法︰**動態︰**若選取此選項，Azure 會自動指派您所選子網路位址空間中的可用位址。 當 NIC 所在的 VM 在進入已停止 (已解除配置) 狀態後重新加以啟動時，Azure 可能會對此 NIC 指派不同的位址。 如果 VM 未曾處於已停止 (已解除配置) 狀態而重新啟動，位址會維持不變。 **靜態︰**若選取此選項，您必須手動指派所選子網路位址空間中的可用 IP 位址。 直到您變更靜態位址或刪除 NIC，靜態位址才會變更。 您可以在建立 NIC 之後，變更指派方法。 Azure DHCP 伺服器會將此位址指派給 VM 作業系統內的 NIC。|
    |網路安全性群組|否| 保持設為 [無]、選取現有的網路安全性群組 (NSG)，或建立 NSG。 NSG 可讓您篩選進出 NIC 的網路流量。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。 若要建立 NSG，請閱讀[建立 NSG](virtual-networks-create-nsg-arm-pportal.md) 一文。 您可以對 NIC 套用零個或一個 NSG。 也可以將零個或一個 NSG 套用至 NIC 所連線到的子網路。 將 NSG 套用至 NIC 及 NIC 所連線的子網路時，有時會發生非預期的結果。 若要針對套用至 NIC 和子網路的 NSG 進行疑難排解，請參閱[針對 NSG 進行疑難排解](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface)一文。|
    |訂用帳戶|是|選取其中一個 Azure [訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 連結 NIC 的 VM 與 VM 所連線的 VNet 必須位於相同的訂用帳戶中。|
    |資源群組|是|選取現有的[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)，或建立一個群組。 NIC 可以位於與其連結的 VM 或 VM 所連線的 VNet 相同或不同的資源群組中。|
    |位置|是|NIC 所連結的 VM 與所連線的 VNet，均須位於相同的[位置](https://azure.microsoft.com/regions) (也稱為區域)。|

入口網站未提供選項讓您在建立 NIC 時對其指派公用 IP 位址，不過，當您使用入口網站來建立 VM 時，它則會對 NIC 指派公用 IP 位址。 若要了解如何在建立 NIC 之後對其新增公用 IP 位址，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。 如果您想要建立具有公用 IP 位址的 NIC，您必須使用 CLI 或 PowerShell 來建立 NIC。

>[!Note]
> Azure 只會在 NIC 連結至 VM 且 VM 第一次啟動之後，將 MAC 位址指派給 NIC。 您無法指定 Azure 指派給 NIC 的 MAC 位址。 在 NIC 遭到刪除或指派給主要 NIC 之主要 IP 組態的私人 IP 位址遭到變更之前，MAC 位址會保持指派給 NIC。 若要深入了解 IP 位址和 IP 組態，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-nics"></a>檢視 NIC 設定

您可以檢視和變更 NIC 的大部分設定。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在顯示的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更設定的 NIC。
4. 下列設定會列在針對您所選 NIC 顯示的刀鋒視窗中︰
    - **概觀︰**提供 NIC 的相關資訊，例如指派給它的 IP 位址、NIC 所連線的 VNet/子網路，以及 NIC 所連結的 VM (如果它已連結至一部 VM)。 下圖顯示名為 **mywebserver256** 之 NIC 的概觀設定：  ![網路介面概觀](./media/virtual-network-network-interface/nic-overview.png) 您可以按一下**資源群組**或**訂用帳戶名稱**旁邊的 ([變更])，以將 NIC 移至不同的資源群組或訂用帳戶。 如果您移動 NIC，則必須同時移動與 NIC 相關的所有資源。 舉例而言，如果 NIC 已連結至 VM，您就必須同時移動 VM 與其他 VM 相關資源。 若要移動 NIC，請閱讀[將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal)一文。 該文會列出必要條件，以及如何使用 Azure 入口網站、PowerShell 和 Azure CLI 來移動資源。
    - **IP 組態︰**系統會對 NIC 的一或多個 IP 組態指派公用與私人 IP 位址。 若要深入了解 NIC 支援的 IP 組態數目上限，請閱讀 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。 每個 IP 組態都有一個指派的私人 IP 位址，而且可以有一個相關聯的公用 IP 位址。 若要對 NIC 新增、變更或刪除 IP 組態，請完成[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文中[將次要 IP 組態新增至 NIC](virtual-network-network-interface-addresses.md#create-ip-config)、[變更 IP 組態](virtual-network-network-interface-addresses.md#change-ip-config)或[刪除 IP 組態](virtual-network-network-interface-addresses.md#delete-ip-config)章節的步驟。 IP 轉送和子網路指派也會在這一節設定。 若要深入了解這些設定，請閱讀本文的[啟用或停用 IP 轉送](#ip-forwarding)和[變更子網路指派](#subnet)章節。
    - **DNS 伺服器︰**您可以指定由 Azure DHCP 伺服器指派給 NIC 的 DNS 伺服器。 NIC 可以從 NIC 所連線的 VNet 繼承設定，或使用自訂設定來覆寫其所連線之 VNet 的設定。 若要修改所顯示的內容，請完成本文之[變更 DNS 伺服器](#dns)一節中的步驟。
    - **網路安全性群組 (NSG)：**顯示與 NIC 相關聯的 NSG (如果有的話)。 NSG 包含輸入和輸出規則，可篩選 NIC 的網路流量。 如果 NSG 與 NIC 相關聯，則會顯示相關聯 NSG 的名稱。 若要修改所顯示的內容，請完成本文的[建立及解除 NSG 與網路介面的關聯](#associate-nsg)一節中的步驟。
    - **屬性︰**顯示有關 NIC 的重要設定，包括其 MAC 位址 (如果 NIC 未連結至 VM，則位址會空白) 以及其所在的訂用帳戶。
    - **有效安全性規則︰**如果NIC 已連結至執行中的 VM，而且 NSG 與 NIC、它所連線的子網路或兩者相關聯，則會列出安全性規則。 若要深入了解所顯示的內容，請閱讀[針對網路安全性群組進行疑難排解](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface)一文。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-nsg.md)一文。
    - **有效路由︰**如果 NIC 已連結至執行中的 VM，則會列出路由。 路由是下列各項的組合：Azure 預設路由、任何使用者定義的路由 (UDR)，以及連線 NIC 之子網路可能存在的任何 BGP 路由。 若要深入了解所顯示的內容，請閱讀[針對路由進行疑難排解](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface)一文。 若要深入了解 Azure 預設值和 UDR，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)一文。
    - **一般 Azure Resource Manager 設定：**若要深入了解一般 Azure Resource Manager 設定，請閱讀[活動記錄](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[存取控制 (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[標籤](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)、[鎖定](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和[自動化指令碼](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)文章。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) 用以檢視訂用帳戶中的 NIC；[az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) 用以檢視 NIC 的設定|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) 用以檢視訂用帳戶中的 NIC 或檢視 NIC 的設定|

## <a name="dns"></a>變更 DNS 伺服器

Azure DHCP 伺服器會將 DNS 伺服器指派給 VM 作業系統內的 NIC。 指派的 DNS 伺服器就是 NIC 的任何 DNS 伺服器設定。 若要深入了解 NIC 的名稱解析設定，請閱讀 [VM 的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)一文。 NIC 可以從 VNet 繼承設定，或使用它自己的唯一設定來覆寫 VNet 的設定。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在顯示的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更設定的 NIC。
4. 在您所選 NIC 的刀鋒視窗中，按一下 [設定] 底下的 [DNS 伺服器]。
5. 按一下：
    - **繼承自虛擬網路 (預設值)**︰選擇此選項，可繼承針對 NIC 連線至的虛擬網路所定義的 DNS 伺服器設定。 在 VNet 層級，定義自訂 DNS 伺服器或 Azure 提供的 DNS 伺服器。 Azure 提供的 DNS 伺服器可為連線至相同 VNet 的資源解析其主機名稱。 對於連線到不同 VNet 的資源，則必須使用 FQDN 來解析。
    - **自訂**︰您可以設定自己的 DNS 伺服器，以解析跨多個 VNet 的名稱。 輸入您要做為 DNS 伺服器之伺服器的 IP 位址。 您指定的 DNS 伺服器位址只能指派給此 NIC，並且會覆寫 NIC 所連線 VNet 的任何 DNS 設定。
6. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-forwarding"></a>啟用或停用 IP 轉送

IP 轉送可讓連結 NIC 的 VM：
- 接收以下網路流量：其目的地不是指派給任何 IP 組態 (已指派給 NIC) 的其中一個 IP 位址。
- 傳送以下網路流量：其來源 IP 位址不同於指派給其中一個 NIC 之 IP 組態的 IP 位址。

必須針對 VM 連結的每個 NIC 啟用此設定，而 NIC 會接收 VM 需要轉送的流量。 已連結多個 NIC 或單一 NIC 的 VM 都可以轉送流量。 IP 轉送雖然是 Azure 設定，但 VM 也必須執行能夠轉送流量的應用程式，例如防火牆、WAN 最佳化及負載平衡應用程式。 當 VM 執行網路應用程式時，VM 通常稱為網路虛擬設備 (NVA)。 您可以檢視 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) 中可立即部署的 NVA 清單。 IP 轉送通常使用於使用者定義的路由。 若要深入了解使用者定義的路由，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)文章。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要啟用或停用 IP 轉送的 NIC。
4. 在所選 NIC 的刀鋒視窗中，按一下 [設定] 區段中的 [IP 組態]。
5. 按一下 [已啟用] 或 [已停用] (預設設定) 來變更設定。
6. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="subnet"></a>變更子網路指派

您可以變更 NIC 所連線至的子網路 (但無法變更 VNet)。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在顯示的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更設定的 NIC。
4. 在所選 NIC 的刀鋒視窗中，按一下 [設定] 底下的 [IP 組態]。 如果所列 IP 組態的私人 IP 位址旁邊有 **(靜態)** 字樣，您必須完成下列步驟以將 IP 位址的指派方法變更為動態。 您必須使用動態指派方法來指派所有的私人 IP 位址，以便變更 NIC 的子網路指派。 如果您使用動態方法來指派位址，請繼續執行步驟 5。 如果您使用靜態指派方法指派了任何位址，請完成下列步驟來將指派方法變更為動態︰
    - 從 IP 組態清單中，按一下您要變更 IP 位址指派方法的 IP 組態。
    - 在為此 IP 組態顯示的刀鋒視窗中，按一下 [動態] 做為 [指派] 方法。
    - 按一下 [儲存] 。
5. 從 [子網路] 下拉式清單中選取您要 NIC 連線至的子網路。
6. 按一下 [儲存] 。 系統就會從新的子網路位址範圍指派新的動態位址。 在對新的子網路指派 NIC 之後，您可以從新的子網路位址範圍指派靜態 IP 位址 (如果您做此選擇的話)。 若要深入了解如何為 NIC 新增、變更和移除 IP 位址，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-nic"></a>刪除 NIC

只要未連結至 VM，您都可以刪除 NIC。 如果 NIC 已連結至 VM，您必須先讓 VM 進入已停止 (已取消配置) 狀態，再讓 NIC 與 VM 中斷連結，然後才能刪除 NIC。 若要讓 NIC 與 VM 中斷連結，請完成[新增或移除網路介面](virtual-network-network-interface-vm.md)一文之[讓 NIC 與虛擬機器中斷連結](virtual-network-network-interface-vm.md#vm-remove-nic)一節中的步驟。 刪除 VM 會讓所有與它連結的 NIC 中斷連結，但不會刪除 NIC。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 以滑鼠右鍵按一下您要刪除的 NIC，然後按一下 [刪除]。
4. 按一下 [是] 以確認刪除 NIC。

當您刪除 NIC 時，會釋出指派給它的所有 MAC 或 IP 位址。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>接續步驟
若要建立具有多個 NIC 或 IP 位址的 VM，請閱讀下列文章︰

**命令**

|Task|工具|
|---|---|
|建立具有多個 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|建立具有多個 IP 位址的單一 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

