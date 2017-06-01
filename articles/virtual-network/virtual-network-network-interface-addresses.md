---
title: "設定 Azure 網路介面的 IP 位址 | Microsoft Docs"
description: "了解如何對 NIC 新增、變更和移除公用與私人 IP 位址。"
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
ms.openlocfilehash: cf7dbc648136897772bbd068687313eec16bed75
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017


---

# <a name="add-change-or-remove-ip-addresses-for-azure-network-interfaces"></a>新增、變更或移除 Azure 網路介面的 IP 位址

了解如何對網路介面 (NIC) 新增、變更和移除公用與私人 IP 位址。 指派給 NIC 的私人 IP 位址可讓 VM 與網際網路通訊，以及讓 VM 與其他連線到 Azure 虛擬網路 (VNet) 的資源進行通訊。 指派給 NIC 的公用 IP 位址則允許從網際網路對 VM 進行輸入通訊。 

如果您需要建立、變更或刪除 NIC，請閱讀 [NIC 設定和工作](virtual-network-network-interface.md)一文。 如果您需要在虛擬機器中新增或移除 NIC，請閱讀[新增或移除 NIC](virtual-network-network-interface-vm.md) 一文。 


## <a name="before"></a>開始之前

在完成本文任一節的任何步驟之前，請先完成下列工作︰

- 檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，以了解公用和私人 IP 位址的限制。
- 使用 Azure 帳戶來登入 Azure 入口網站、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果要使用 PowerShell 命令執行本文中的工作，請完成[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟來安裝和設定 Azure PowerShell。 請確定您已安裝最新版的 Azure PowerShell commandlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果要使用 PowerShell 命令列介面 (CLI) 執行本文中的工作，請完成[如何安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟來安裝和設定 Azure CLI。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。

## <a name="about"></a>關於 NIC 和 IP 位址

您可以對每個 NIC 指派多個私人和公用 IP 位址，只要不超過 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)即可。 將多個 IP 位址指派給 NIC 對有些案例很有幫助，例如︰
- 在單一伺服器上，以不同 IP 位址和 SSL 憑證裝載多個網站或服務。
- 做為網路虛擬設備 (例如防火牆或負載平衡器) 的 VM。
- 能夠將任何 NIC 的任何私人 IP 位址新增到 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。 若要深入了解如何負載平衡多個 IP 設定，請參閱[負載平衡多個 IP 組態](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)文章。

系統會在 IP 組態中指派 IP 位址。 系統一律會對 NIC 指派**主要** IP 組態，但您也可以對它指派多個**次要**組態。 系統會在每個 IP 組態中指派下列位址類型的其中一種或同時指派兩者︰
- **私人︰**私人 IP 位址可讓 VM 與其他資源通訊，這些資源會連線到 NIC 所在的 VNet。 IP 組態必須有一個指派的私人 IP 位址。 Azure DHCP 伺服器會將 NIC 之主要 IP 組態的私人 IP 位址指派給 VM 作業系統內的 NIC。 私人 IP 位址也可讓 VM 對網際網路進行輸出通訊。 輸出連線會進行來源網路位址轉譯 (SNAT)。 若要深入了解 Azure 輸出網際網路連線能力，請閱讀[Azure 輸出網際網路連線能力](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 您無法從網際網路對 VM 的私人 IP 位址進行輸入通訊。
- **公用︰**公用 IP 位址可從網際網路對 VM 進行輸入連線。 對網際網路輸出的連線則不會進行 SNAT。 您可以 (但不需要) 對 IP 組態指派公用 IP 位址。 若要深入了解公用 IP 位址，請閱讀[公用 IP 位址](virtual-network-public-ip-address.md)一文。

您可以指派給 NIC 的公用和私人 IP 位址數目有所限制。 如需詳細資訊，請閱讀 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文。

您可以使用下列配置方法來指派公用和私人 IP 位址︰
- **動態︰**系統預設會指派動態的私人和公用 IP 位址。 如果 VM 在進入已停止 (已解除配置) 狀態後又重新啟動，動態位址將會變更。 如果您希望在 VM 的存留期內都不要變更 IP 位址，請使用靜態位址。
- **靜態︰**直到您刪除 VM，靜態位址才會變更。 您所指派的靜態私人 IP 位址，是來自 NIC 所連結之子網路的位址空間。 若要深入了解 Azure 如何指派靜態的公用 IP 位址，請閱讀[公用 IP 位址](virtual-network-public-ip-address.md)一文。

## <a name="create-ip-config"></a>新增 IP 位址

您可以將所需的多個 IP 位址新增至 NIC，但不得超過 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文所列的限制。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要新增 IP 位址的 NIC。
4. 在所選 NIC 的刀鋒視窗中，按一下 [設定] 區段中的 [IP 組態]。
5. 在針對 IP 組態開啟的刀鋒視窗中按一下 [+ 新增]。
6. 指定下列各項，然後按一下 [確定]，以關閉 [新增 IP 組態] 刀鋒視窗：

    |設定|必要？|詳細資料|
    |---|---|---|
    |名稱|是|對 NIC 而言必須是唯一的|
    |類型|是|由於您要將 IP 組態新增至現有的 NIC，而且每個 NIC 都必須有一個主要 IP 組態，所以您只能選擇 [次要]。|
    |私人 IP 位址指派方法|是|如果 VM 在處於已停止 (已解除配置) 狀態之後重新啟動，**動態**位址可能變更。 Azure 會指派 NIC 所連線子網路之位址空間中可用的位址。 直到 NIC 遭到刪除，才會釋出**靜態**位址。 指定子網路位址空間範圍中其他 IP 組態目前未使用的 IP 位址。|
    |公用 IP 位址|否|**已停用：**IP 組態目前沒有相關聯的公用 IP 位址資源。 **已啟用：**選取現有的公用 IP 位址，或建立一個新的。 若要了解如何建立公用 IP 位址，請閱讀[公用 IP 位址](virtual-network-public-ip-address.md#create)一文。|
7. 請完成[將多個 IP 位址指派給虛擬機器](virtual-network-multiple-ip-addresses-portal.md#os-config)一文中的指示，以手動方式將次要私人 IP 位址新增至 VM 作業系統。 請勿將任何公用 IP 位址新增至 VM 作業系統。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-config"></a>變更 IP 位址設定

您可能需要變更 IP 位址的指派方法、變更靜態 IP 位址，或變更指派給 NIC 的公用 IP 位址。 如果您要變更與 VM 中次要 NIC 相關聯之次要 IP 組態的私人 IP 位址 (深入了解[主要和次要 NIC](virtual-network-network-interface-vm.md#about))，請先讓 VM 進入停止 (已取消配置) 狀態，再完成下列步驟︰ 

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要檢視或變更 IP 位址設定的 NIC。
4. 在所選 NIC 的刀鋒視窗中，按一下 [設定] 區段中的 [IP 組態]。
5. 在針對 IP 組態開啟的刀鋒視窗中，按一下清單中您要修改的 IP 位址。
6. 使用本文之[新增 IP 組態](#create-ip-config)一節中步驟 6 的設定資訊，視需要變更設定。 按一下 [儲存] 以關閉您所變更之 IP 組態的刀鋒視窗。

>[!NOTE]
>如果主要 NIC 有多個 IP 組態，而且您變更主要 IP 組態的私人 IP 位址，您必須以手動方式將所有次要 IP 位址重新指派給 Windows 內的 NIC (對 Linux 而言並非必要)。 若要以手動方式將 IP 位址指派給作業系統內的 NIC，請閱讀[將多個 IP 位址指派給虛擬機器](virtual-network-multiple-ip-addresses-portal.md#os-config)一文。 請勿將任何公用 IP 位址新增至 VM 作業系統。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-ip-config"></a>移除 IP 位址

您可以從 NIC 移除私人和公用的 IP 位址，但一律必須對 NIC 指派至少一個私人 IP 位址。

1. 使用具備您訂用帳戶網路參與者角色 (最低) 權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色和權限指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「網路介面」。 當「網路介面」出現於搜尋結果時，按一下它。
3. 在出現的 [網路介面] 刀鋒視窗中，按一下您要從中移除 IP 位址的 NIC。
4. 在所選 NIC 的刀鋒視窗中，按一下 [設定] 區段中的 [IP 組態]。
5. 以滑鼠右鍵按一下您想要刪除的次要 IP 組態 (您無法刪除主要組態)，按一下 [刪除]，然後按一下 [是] 以確認刪除。 如果組態有相關聯的公用 IP 位址資源，此資源會與 IP 組態解除關聯，但不會刪除資源。
6. 關閉 [IP 組態] 刀鋒視窗。

**命令**

|工具|命令|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>接續步驟
若要建立具有多個 NIC 或 IP 位址的 VM，請閱讀下列文章︰

**命令**

|Task|工具|
|---|---|
|建立具有多個 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|建立具有多個 IP 位址的單一 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

