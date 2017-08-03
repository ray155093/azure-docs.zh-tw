---
title: "在 Azure 虛擬機器中新增或移除網路介面 | Microsoft Docs"
description: "了解如何在虛擬機器中新增網路介面或移除網路介面。"
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
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 4273a8ec344c83d36d3383f991d1764d2285beeb
ms.contentlocale: zh-tw
ms.lasthandoff: 07/18/2017

---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>在虛擬機器中新增或移除網路介面

了解如何在建立 VM 時新增現有的網路介面，或在處於已停止 (已取消配置) 狀態的現有 VM 中新增或移除網路介面。 網路介面可讓 Azure 虛擬機器 (VM) 與網際網路、Azure 以及內部部署資源進行通訊。 一個 VM 可以有一或多個網路介面。 

如果您需要新增、變更或移除網路介面的 IP 位址，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。 如果您需要建立、變更或刪除網路介面，請閱讀[管理網路介面](virtual-network-network-interface.md)一文。

## <a name="before"></a>開始之前

在完成本文任一節的任何步驟之前，請先完成下列工作︰

- 檢閱 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章，以了解每個 Linux 和 Windows VM 大小所支援的網路介面數目。
- 使用 Azure 帳戶來登入 Azure 入口網站、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果要使用 PowerShell 命令執行本文中的工作，請完成[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟來安裝和設定 Azure PowerShell。 請確定您已安裝最新版的 Azure PowerShell commandlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果要使用 PowerShell 命令列介面 (CLI) 執行本文中的工作，請完成[如何安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步驟來安裝和設定 Azure CLI。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。

## <a name="about"></a>關於網路介面和 VM

您可以在建立 VM 時將現有的網路介面新增 (連接) 至該 VM，但前提是該網路介面目前並未連接至其他 VM。 您可以在現有 VM 中新增網路介面或從中移除 (中斷連接) 網路介面，前提是該 VM 處於已停止 (已取消配置) 狀態。 如果您使用 Azure 入口網站來建立 VM，入口網站就會以預設設定為您建立網路介面。 入口網站無法讓您︰

- 在建立 VM 時指定要新增的現有網路介面
- 建立具有多個網路介面的 VM
- 指定網路介面的名稱 (入口網站會建立具預設名稱的網路介面)

您可以使用 Azure PowerShell 或 CLI，以無法使用於入口網站的所有先前屬性建立網路介面或 VM。 在完成下列各節中的工作之前，請考量下列條件約束和行為︰

- 所有 VM 大小至少都會支援兩個網路介面，但某些 VM 大小可支援兩個以上的網路介面。 在過去，某些 VM 大小僅支援一個網路介面。 若要了解每個 VM 大小所支援的網路介面數目，請閱讀 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 
- 在過去，網路介面僅可新增至支援多個網路介面且至少以兩個網路介面建立的 VM。 您無法將網路介面新增至使用一個網路介面建立的 VM，即使該 VM 大小支援多個網路介面也一樣。 相反地，由於以至少兩個網路介面所建立的 VM 一定要有至少兩個網路介面，因此您只能從至少具有三個網路介面的 VM 中移除網路介面。 以上這些限制已不再適用。 您現在能夠以任何數目的網路介面建立 VM (最多可達 VM 大小所支援的數目)，以及新增或移除任何數目的網路介面 (從處於已停止 (已取消配置) 狀態的 VM)，只要 VM 一律至少具有一個網路介面即可。
- 根據預設，會將 VM 中的第一個網路介面定義為「主要」網路介面。 VM 中的所有其他網路介面均為「次要」網路介面。
- Azure DHCP 伺服器會對主要網路介面指派預設閘道，但不會對次要網路介面加以指派。 由於系統未對次要網路介面指派預設閘道，因此根據預設，它們無法與其子網路外的資源進行通訊。 若要讓 Windows VM 中的次要網路介面能夠與其子網路外的資源進行通訊，請從 Windows 命令列使用 `route add` 命令對作業系統新增路由。 對於 Linux VM，由於其預設行為會使用弱式主機路由，因此建議您將次要網路介面的流量限制在單一子網路中。 如果您需要對次要網路介面的子網路外部進行連線，請啟用原則式路由，以確保輸入和輸出流量都使用同一個網路介面。
- 根據預設，來自 VM 的所有輸出流量都會送出指派給主要網路介面之主要 IP 組態的 IP 位址。 您可以控制要對 VM 作業系統內的輸出流量使用哪個 IP 位址，但根據預設，流量會透過主要網路介面傳輸。
- 在過去，相同可用性設定組中的所有 VM 都必須具有單一或多個網路介面。 具有任意多個 (最多可達 VM 大小所支援的數目) 網路介面的 VM 現在可存在於相同的可用性設定組中。 然而，只有在建立 VM 時，才能將 VM 新增到可用性設定組。 若要深入了解可用性設定組，請閱讀[在 Azure 中管理 VM 的可用性](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)一文。
- 雖然相同 VM 中的網路介面可以連線至 VNet 中不同的子網路，但這些網路介面全都必須連線至相同的 VNet。
- 您可以將任何主要或次要網路介面之任何 IP 組態的任何 IP 位址新增至 Azure Load Balancer 後端集區。 在過去，只能將主要網路介面的主要 IP 位址新增到後端集區。 若要深入了解 IP 位址和組態，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。
- 刪除 VM 並不會刪除與其連接的網路介面。 刪除 VM 時，會中斷網路介面與該 VM 的連接。 您可以將網路介面新增至其他 VM，也可以刪除它們。

## <a name="vm-create"></a>將現有的網路介面新增至新的 VM
當您透過入口網站建立 VM 時，入口網站會以預設設定為您建立網路介面，並將其連接至該 VM。 您無法將現有的網路介面新增至新的 VM，或使用 Azure 入口網站建立具有多個網路介面的 VM。 這兩項作業都可使用 CLI 或 PowerShell 來進行。 您所建立的 VM 大小能支援多少個網路介面，您在 VM 中就能新增這個數目的網路介面。 若要深入了解每個 VM 大小所支援的網路介面數目，請閱讀 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 您在某個 VM 中新增的網路介面目前無法連接至另一個 VM。 若要深入了解如何建立網路介面，請閱讀[管理網路介面](virtual-network-network-interface.md#create-nic)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>將現有的網路介面新增至現有的 VM

您要在其中新增網路介面的 VM 大小能支援多少個網路介面，您在 VM 中就能新增這個數目的網路介面。 若要了解每個 VM 大小所支援的網路介面數目，請閱讀 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 您要在其中新增網路介面的 VM 必須支援多個網路介面且處於已停止 (已解除配置) 狀態。 您想要新增的網路介面目前無法連接至另一個 VM。 您無法使用 Azure 入口網站，將網路介面新增至現有的 VM。 您必須使用 CLI 或 PowerShell 來將網路介面新增至現有的 VM。

|工具|命令|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|[將 NIC 新增至 VM](../virtual-machines/windows/multiple-nics.md#add-a-nic-to-an-existing-vm) 的詳細步驟|

## <a name="vm-view-nic"></a>檢視 VM 的網路介面

您可以檢視目前連接至 VM 的網路介面，以了解每個網路介面的組態，以及指派給每個網路介面的 IP 位址。 

1. 使用具備您訂用帳戶之擁有者、參與者或網路參與者角色的帳戶登入 [Azure 入口網站](https://portal.azure.com)。 請閱讀 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，深入了解如何將角色指派給帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬機器」。 當「虛擬機器」出現於搜尋結果時，按一下它。
3. 在顯示的 [虛擬機器] 刀鋒視窗中，按一下您要檢視其網路介面的 VM 名稱。
4. 在針對您所選 VM 出現的 [虛擬機器] 刀鋒視窗中，於 [設定] 區段按一下 [網路介面]。 若要了解網路介面設定以及如何變更它們，請閱讀[管理網路介面](virtual-network-network-interface.md)一文。 若要了解如何新增、變更或移除已指派給網路介面的 IP 位址，請閱讀[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)一文。

**命令**

|工具|命令|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a>從 VM 移除網路介面

您想要從中移除網路介面的 VM 必須處於已停止 (已解除配置) 狀態，而且目前必須至少有兩個連接的網路介面。 您可以移除任何網路介面，但 VM 一律至少必須有一個連接的網路介面。 如果您移除主要網路介面，Azure 會將主要屬性指派給連接至 VM 的時間最久的網路介面。 您可以自行指定任何網路介面作為主要網路介面。 您無法從 VM 中移除網路介面，也無法使用 Azure 入口網站設定網路介面的主要屬性 (雖然您可以使用 PowerShell 或 CLI 來完成這兩項作業)。 

**命令**

|工具|命令|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|[從 VM 移除 NIC](../virtual-machines/windows/multiple-nics.md#remove-a-nic-from-an-existing-vm) 的詳細步驟|
## <a name="next-steps"></a>接續步驟
若要建立具有多個網路介面或 IP 位址的 VM，請閱讀下列文章：

**命令**

|Task|工具|
|---|---|
|建立具有多個 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|建立具有多個 IP 位址的單一 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

