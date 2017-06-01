---
title: "在 Azure 虛擬機器中新增或移除網路介面 | Microsoft Docs"
description: "了解如何在虛擬機器中新增或移除網路介面 (NIC)。"
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
ms.openlocfilehash: 0d609b20040572e3fb371a277603109d64a0ba37
ms.contentlocale: zh-tw
ms.lasthandoff: 05/12/2017


---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>在虛擬機器中新增或移除網路介面

了解如何在建立 VM 時新增現有的網路介面 (NIC)，或在已進入停止 (取消配置) 狀態的現有 VM 中新增或移除 NIC。 NIC 可讓 Azure 虛擬機器 (VM) 與網際網路、Azure 以及內部部署資源進行通訊。 一個 VM 可以有一或多個 NIC。 

如果您需要新增、變更或移除 NIC 的 IP 位址，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。 如果您需要建立、變更或刪除 NIC，請閱讀 [NIC 設定和工作](virtual-network-network-interface.md)一文。

## <a name="before"></a>開始之前

在完成本文任一節的任何步驟之前，請先完成下列工作︰

- 檢閱 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章，以了解每個 Linux 和 Windows VM 大小所支援的 NIC 數目。
- 使用 Azure 帳戶來登入 Azure 入口網站、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果要使用 PowerShell 命令執行本文中的工作，請完成[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟來安裝和設定 Azure PowerShell。 請確定您已安裝最新版的 Azure PowerShell commandlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果要使用 PowerShell 命令列介面 (CLI) 執行本文中的工作，請完成[如何安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟來安裝和設定 Azure CLI。 請確定您已安裝最新版的 Azure CLI。若要取得 CLI 命令的說明，請輸入 `az <command> --help`。

## <a name="about"></a>關於 NIC 和 VM

您可以在建立 VM 時將現有 NIC 新增 (連結) 至該 VM，但前提是該 NIC 目前未連結至其他 VM。 您可以在現有 VM 中新增 NIC 或從中移除 (中斷連結) NIC，前提是該 VM 已進入停止 (取消配置) 狀態。 如果您使用 Azure 入口網站來建立 VM，入口網站會以預設設定為您建立 NIC。 入口網站無法讓您︰

- 在建立 VM 時指定要新增的現有 NIC
- 建立具有多個 NIC 的 VM
- 指定 NIC 的名稱 (入口網站會使用預設名稱建立 NIC)

您可以使用 Azure PowerShell 或 CLI，以無法使用於入口網站的所有先前屬性建立 NIC 或 VM。 在完成下列各節中的工作之前，請考量下列條件約束和行為︰

- 所有 VM 大小都至少支援兩個 NIC，但某些 VM 大小可支援兩個以上的 NIC。 在過去，某些 VM 大小僅支援一個 NIC。 若要了解每個 VM 大小所支援的 NIC 數目，請閱讀 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 
- 在過去，NIC 僅可新增至支援多個 NIC 並至少以兩個 NIC 建立的 VM。 您不可將 NIC 新增至以一個 NIC 建立的 VM，即使 VM 大小支援多個 NIC。 相反地，由於以至少兩個 NIC 所建立的 VM 一定要有至少兩個 NIC，因此您只能從至少具有三個 NIC 的 VM 中移除 NIC。 以上這些限制已不再適用。 您現在能夠以任何數目的 NIC 建立 VM (可達 VM 大小所支援的數目) 並新增或移除任何數目的 NIC (從已進入停止 (取消配置) 狀態的 VM 中)，只要 VM 總是至少具有一個 NIC 即可。
- 根據預設，VM 中的第一個 NIC 會定義為「主要」NIC。 VM 中的其他所有 NIC 則為「次要」NIC。
- Azure DHCP 伺服器會對主要 NIC 指派預設閘道，但不會對次要 NIC 指派。 由於系統未對次要 NIC 指派預設閘道，因此根據預設，它們無法與其子網路外的資源通訊。 若要讓 Windows VM 中的次要 NIC 能夠與其子網路外的資源進行通訊，請從 Windows 命令列使用 `route add` 命令對作業系統新增路由。 對於 Linux VM，因為其預設行為是使用弱式主機路由，建議您將次要 NIC 的流量限制在單一子網路中。 如果您需要對次要 NIC 的子網路外部進行連線，請啟用原則式路由，以確保輸入和輸出流量都使用同一個 NIC。
- 根據預設，來自 VM 的所有輸出流量會送出指派給主要 NIC 之主要 IP 組態的 IP 位址。 您可以控制要對 VM 作業系統內的輸出流量使用哪個 IP 位址，但根據預設，流量會透過主要 NIC 來傳輸。
- 在過去，相同可用性設定組中的所有 VM 都必須有單一或多個 NIC。 具有任意多個 (最多可達 VM 大小所支援的數目) NIC 的 VM 現在可存在於相同的可用性設定組中。 然而，只有在建立 VM 時，才能將 VM 新增到可用性設定組。 若要深入了解可用性設定組，請閱讀[在 Azure 中管理 VM 的可用性](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)一文。
- 雖然相同 VM 中的 NIC 可以連線至 VNet 中不同的子網路，但這些 NIC 必須全部連線至相同的 VNet。
- 您可以將任何主要或次要 NIC 之任何 IP 組態的任何 IP 位址新增至 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。 若要深入了解 IP 位址和組態，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。
- 刪除 VM 並不會刪除與其連結的 NIC。 刪除 VM 時，會將 NIC 與該 VM 中斷連結。 您可以將 NIC 新增至其他 VM，也可以刪除 NIC。

## <a name="vm-create"></a>對新的 VM 新增現有 NIC
當您透過入口網站來建立 VM 時，入口網站會以預設設定為您建立 NIC，並將其連結至該 VM。 您無法將現有 NIC 新增至新的 VM，或使用 Azure 入口網站建立具有多個 NIC 的 VM。 這兩項作業都可使用 CLI 或 PowerShell 來進行。 您所建立的 VM 大小能支援多少 NIC，您在 VM 中就能新增這個數目的 NIC。 若要深入了解每個 VM 大小所支援的 NIC 數目，請參閱 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小文章。 您在某個 VM 中新增的 NIC 目前並無法連結至另一個 VM。 若要深入了解如何建立 NIC，請閱讀 [NIC 設定和工作](virtual-network-network-interface.md#create-nic)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>對現有 VM 新增現有 NIC

您要在其中新增 NIC 的 VM 大小能支援多少 NIC，您在 VM 中就能新增這個數目的 NIC。 若要了解每個 VM 大小所支援的 NIC 數目，請閱讀 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 您要在其中新增 NIC 的 VM 必須支援多個 NIC 並處於已停止 (已解除配置) 狀態。 您想要新增的 NIC 目前並無法連結至另一個 VM。 您無法使用 Azure 入口網站，將 NIC 新增至現有 VM。 您必須使用 CLI 或 PowerShell 來將 NIC 新增至現有 VM。

|工具|命令|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-view-nic"></a> 檢視 VM 的 NIC

您可以檢視目前連結至 VM 的 NIC，以了解每個 NIC 的組態，以及指派給每個 NIC 的 IP 位址。 

1. 使用具備您訂用帳戶之擁有者、參與者或網路參與者角色的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬機器」。 當「虛擬機器」出現於搜尋結果時，按一下它。
3. 在出現的 [虛擬機器] 刀鋒視窗中，按一下您要檢視 NIC 之 VM 的名稱。
4. 在針對您所選 VM 出現的 [虛擬機器] 刀鋒視窗中，於 [設定] 區段按一下 [網路介面]。 若要了解 NIC 設定以及要如何加以變更，請閱讀 [NIC 設定和工作](virtual-network-network-interface.md)一文。 若要了解如何為 NIC 新增、變更或移除所指派的 IP 位址，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> 從 VM 中移除 NIC

您想要從中移除 NIC 的 VM 必須處於已停止 (已解除配置) 狀態，而且目前必須至少有兩個連結的 NIC。 您可以移除任何 NIC，但 VM 必須永遠連結至少一個 NIC。 如果移除主要 NIC，Azure 會將主要屬性指派給連結至 VM 的時間最久的 NIC。 您可以自行指定任何 NIC 作為主要 NIC。 您無法從 VM 中移除 NIC，也無法使用 Azure 入口網站設定 NIC 的主要屬性 (雖然您可以使用 PowerShell 或 CLI 來完成這兩項作業)。 

**命令**

|工具|命令|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>接續步驟
若要建立具有多個 NIC 或 IP 位址的 VM，請閱讀下列文章︰

**命令**

|Task|工具|
|---|---|
|建立具有多個 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|建立具有多個 IP 位址的單一 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

