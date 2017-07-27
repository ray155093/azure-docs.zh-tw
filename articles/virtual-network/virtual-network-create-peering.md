---
title: "建立 Azure 虛擬網路對等互連 | Microsoft Docs"
description: "了解如何在兩個虛擬網路之間建立虛擬網路對等互連。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: jdial;narayan;annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 666165115e80a39d02386719b0d7e64d7ae17749
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-virtual-network-peering"></a>建立虛擬網路對等互連

在本教學課程中，您會了解如何在兩個虛擬網路之間建立虛擬網路對等互連。 對等互連兩個虛擬網路，可讓不同虛擬網路中的資源彼此通訊，且通訊時會有相同的頻寬和延遲，彷彿這些資源是位於相同的虛擬網路中。 只能在存在於相同 Azure 區域中的兩個虛擬網路之間建立虛擬網路對等互連。 若要深入了解虛擬網路對等互連，請閱讀[虛擬網路對等互連](virtual-network-peering-overview.md)概觀一文。 

如果您需要連線存在於不同 Azure 區域中的虛擬網路，您可以使用 Azure [VPN 閘道](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)來連線虛擬網路。 

您可以使用 [Azure 入口網站](#portal)、Azure [PowerShell](#powershell)、Azure [命令列介面](#cli) (CLI) 或 [Azure Resource Manager 範本](#template)，建立虛擬網路對等互連。 按一下任何先前的工具連結，直接前往使用您所選工具建立虛擬網路對等互連的步驟。

## <a name="portal"></a>建立對等互連 - Azure 入口網站

完成下列步驟，在透過 Resource Manager 部署且存在於相同訂用帳戶的兩個虛擬網路之間，建立虛擬網路對等互連。 您也可以[對等互連不同訂用帳戶中的兩個虛擬網路](#different-subscriptions)或[對等互連一個虛擬網路 (Resource Manager) 與另一個虛擬網路 (傳統)](#different-models)。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
2. 依序按一下 [新增]、[網路] 及 [虛擬網路]。
3. 在 [虛擬網路] 刀鋒視窗的 [選取部署模型] 方塊中，讓 [Resource Manager] 保持選取狀態，然後按一下 [建立]。
4. 在 [建立虛擬網路] 刀鋒視窗上，輸入或選取下列設定的值，然後按一下 [建立]：
    - **名稱**：*myVnet1*
    - **位址空間**：*10.0.0.0/16*
    - **子網路名稱**：*預設值*
    - **子網路位址範圍**：*10.0.0.0/24*
    - **訂用帳戶**︰選取您的訂用帳戶
    - **資源群組**：選取 [新建] 並輸入 *myResourceGroup*
    - **位置**：*美國東部*
5. 再次完成步驟 2-4 並在步驟 4 中指定下列值：
    - **名稱**： *myVnet2*
    - **位址空間**：*10.1.0.0/16*
    - **子網路名稱**：*預設值*
    - **子網路位址範圍**：*10.1.0.0/24*
    - **訂用帳戶**︰選取您的訂用帳戶
    - **資源群組**：選取 [使用現有] 並選取 *myResourceGroup*
    - **位置**：*美國東部*
6. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myResourceGroup*。 當搜尋結果中出現 **MyResourceGroup** 時，按一下該項目。 **myresourcegroup** 資源群組的刀鋒視窗隨即出現。 資源群組包含在前述步驟中建立的兩個虛擬網路。
7. 按一下 [myVNet1]。
8. 在顯示的 [myVnet1] 刀鋒視窗，從刀鋒視窗左側的垂直選項清單中按一下 [對等互連]。
9. 在顯示的 [myVnet1 - 對等互連] 刀鋒視窗中，按一下 [+ 新增]
10. 在顯示的 [新增對等互連] 刀鋒視窗中，輸入或選取下列選項，然後按一下 [確定]：
     - **名稱**：*myVnet1ToMyVnet2*
     - **虛擬網路部署模型**：選取 [Resource Manager]。 
     - **訂用帳戶**︰選取您的訂用帳戶
     - **虛擬網路**：按一下 [選擇虛擬網路]，然後按一下 [myVnet2]。
     - **允許虛擬網路存取**：確定已選取 [啟用]。
    本教學課程中不會使用其他設定。 若要了解所有對等互連設定，請閱讀[管理虛擬網路對等互連](virtual-network-manage-peering.md#create-peering)。
11. 按一下上一個步驟中的 [確定] 後，[新增對等互連] 刀鋒視窗隨即關閉，而且您會再次看到 [myVnet1 - 對等互連] 刀鋒視窗。 幾秒之後，您建立的對等互連會出現在刀鋒視窗中。 您建立之 **myVnet1ToMyVnet2** 對等互連的 [對等互連狀態] 資料行中列出 [已起始]。 您已將 Vnet1 對等互連到 Vnet2，但您現在必須將 Vnet2 對等互連到 Vnet1。 必須以建立雙線的對等互連，虛擬網路中的資源才能彼此通訊。
12. 針對 myVnet2 再次完成步驟 6-11。  將對等互連命名為 *myVnet2ToMyVnet1*。
13. 按一下 [確定] 來建立 MyVnet2 的對等互連幾秒之後，您剛建立之 **myVnet2ToMyVnet1** 對等互連的 [對等互連狀態] 資料行中會列出 [已連線]。
14. 針對 myVnet1 再次完成步驟 6-8。 **myVnet1ToVNet2** 對等互連的 [對等互連狀態] 現在也是 [已連線]。 您在對等互連中兩個虛擬網路的 [對等互連狀態] 資料行中看到 [已連線] 之後，對等互連變已建立成功。
15. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
16. **選擇性︰**若要刪除您在本教學課程中建立的資源，請完成本文之[刪除資源](#delete-portal)一節中的步驟。

您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="cli"></a>建立對等互連 - Azure CLI

下列指令碼：

- 需要 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

1. 建立一個資源群組和兩個虛擬網路。
    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    #
    ```
2. 在兩個虛擬網路之間建立虛擬網路對等互連。
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    #
    ```
3. 在指令碼執行後，檢閱每個虛擬網路的對等互連。 複製下列命令，然後將它貼入您的命令視窗中：

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    #
    ```
再次執行前一個命令，以 *myVnet2* 取代 *myVnet1*。 兩個命令的輸出會在 [對等互連] 資料行中顯示 [已連線]。
4. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
5. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-cli)中的步驟。

您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。
 
## <a name="powershell"></a>建立對等互連 - PowerShell

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 若要啟動 PowerShell 工作階段，請移至 [開始]，輸入 **powershell**，然後按一下 [PowerShell]。
3. 在 PowerShell 視窗中，輸入 `login-azurermaccount` 命令來登入 Azure。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
4. 在瀏覽器中，複製下列指令碼，然後以滑鼠右鍵按一下 PowerShell 視窗，以執行可建立一個資源群組和兩個虛擬網路的指令碼：
    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    #
    ```
5. 在瀏覽器中，複製下列指令碼，然後以滑鼠右鍵按一下 PowerShell 視窗，以執行可在兩個虛擬網路之間建立虛擬網路對等互連的指令碼：
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    #
    ```
6. 若要檢閱虛擬網路的子網路，請複製下列命令，然後貼到 PowerShell 視窗：

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    #
    ```

再次執行前一個命令，以 *myVnet2* 取代 *myVnet1*。 兩個命令的輸出會在 [對等互連] 資料行中顯示 [已連線]。
7. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
8. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-powershell)中的步驟。

您在任何一個虛擬網路中建立的任何 Azure 資源現在能夠透過其 IP 位址彼此通訊。 如果您使用虛擬網路的預設 Azure 名稱解析，則虛擬網路中的資源無法跨虛擬網路解析名稱。 如果您想要跨對等互連中的虛擬網路解析名稱，您必須建立自己的 DNS 伺服器。 了解如何設定[使用自己的 DNS 伺服器進行名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="template"></a>建立對等互連 - Resource Manager 範本

1. 參考[建立虛擬網路對等互連](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) Resource Manager 範本。 範本會提供使用 Azure 入口網站、PowerShell 或 Azure CLI 來部署範本的指示。 登入您選擇的工具，以使用具備建立虛擬網路對等互連之必要權限的帳戶來部署範本。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
2. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
3. **選擇性︰**若要刪除您在本教學課程中建立的資源，請使用 Azure 入口網站、PowerShell 或 Azure CLI 來完成本文之[刪除資源](#delete)一節中的步驟。

## <a name="different-models"></a>對等互連透過不同部署模型建立的虛擬網路

上一節中的步驟說明如何透過 Resource Manager 部署模型，建立兩個虛擬網路之間的虛擬網路對等互連。 您也可以建立虛擬網路 (Resource Manager) 與虛擬網路 (傳統) 之間的對等互連。 您無法在透過傳統部署模型建立的兩個虛擬網路之間建立虛擬網路對等互連。 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

若要建立虛擬網路對等互連，請完成本文[入口網站](#portal)一節中的步驟 1-11，但在步驟 3 中建立 myVnet2 時選擇**傳統**部署模型。

建立虛擬網路 (Resource Manager) 與虛擬網路 (傳統) 之間的虛擬網路對等互連時，您只要設定從虛擬網路 (Resource Manager) 至虛擬網路 (傳統) 的對等互連。 建立虛擬網路 (Resource Manager) 的對等互連之後，其對等狀態為 [更新中]。 狀態會在幾秒後自動變更為 [已連線]，因為您不需要建立虛擬網路 (傳統) 的對等互連。 

> [!NOTE]
> 雖然未涵蓋在本教學課程中，但您可以在本文的 [Azure CLI](#cli) 和 [PowerShell](#powershell) 章節中調整指令碼，以反映這本節涵蓋的差異。

## <a name="different-subscriptions"></a>對等互連不同訂用帳戶中的虛擬網路

前幾節中的步驟說明如何在相同訂用帳戶中的兩個虛擬網路之間建立虛擬網路對等互連。 您也可以在不同訂用帳戶中的兩個虛擬網路之間建立對等互連，前提是兩個訂用帳戶與相同的 Azure Active Directory 租用戶產生關聯。 如果您還沒有 AD 租用戶，您可以快速地[建立一個](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch)。 如果訂用帳戶與不同的 Active Directory 租用戶相關聯，您無法在不同訂用帳戶中的虛擬網路之間建立虛擬網路對等互連。 視虛擬網路透過哪種部署模型來建立，建立對等互連的步驟稍有不同，如下所示：

### <a name="different-subscriptions-same-deployment-model"></a>透過 Resource Manager 建立的兩個虛擬網路

1. 請完成本文[入口網站](#portal)一節中的步驟 1-7，但是在步驟 5 中為 myVnet2 選擇的訂用帳戶，不同於在步驟 4 中為 myVnet1 選擇的訂用帳戶 (與相同的 Azure Active Directory 租用戶相關聯)。
2. 在顯示的 [myVnet1] 刀鋒視窗，從刀鋒視窗左側的垂直選項清單中按一下 [存取控制 (IAM)]。
3. 在顯示的 [myVnet1 - 存取控制 (IAM)] 刀鋒視窗中，按一下 [+ 新增]。
4. 在顯示的 [新增權限] 刀鋒視窗中，選取 [角色] 方塊中的 [網路參與者]。
5. 在 [選取] 方塊中，選取使用者名稱，或輸入電子郵件地址以搜尋一個。 顯示的使用者清單來自與您設定對等互連之虛擬網路相同的 Azure Active Directory 租用戶。
6. 按一下 [儲存] 。
7. 在 [myVnet1 - 存取控制 (IAM)] 刀鋒視窗，從刀鋒視窗左側的垂直選項清單中按一下 [屬性]。 複製 [資源識別碼]，如下列範例所示：/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet1
8. 針對 myVnet2 完成本節的步驟 1 - 7。
9. 若要確保授權啟用成功，請登出 Azure，然後重新登入。 如果您對兩個虛擬網路使用不同的帳戶，請登出這兩個帳戶並重新登入兩者。
10. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myResourceGroup*。 當搜尋結果中出現 **MyResourceGroup** 時，按一下該項目。 **myresourcegroup** 資源群組的刀鋒視窗隨即出現。 資源群組包含在前述步驟中建立的兩個虛擬網路。
11. 按一下 [myVNet1]。
12. 在顯示的 [myVnet1] 刀鋒視窗，從刀鋒視窗左側的垂直選項清單中按一下 [對等互連]。
13. 在顯示的 [myVnet1 - 對等互連] 刀鋒視窗中，按一下 [+ 新增]
14. 在顯示的 [新增對等互連] 刀鋒視窗中，輸入或選取下列選項，然後按一下 [確定]：
     - **名稱**：*myVnet1ToMyVnet2*
     - **虛擬網路部署模型**︰選取 [傳統]。 
     - **我知道我的資源識別碼**：核取此方塊。
     - **資源識別碼**： 輸入 myVnet2 的資源識別碼。 只有核取 [我知道我的資源識別碼] 核取方塊，才會出現此方塊。
     - **允許虛擬網路存取**：確定已選取 [啟用]。
    本教學課程中不會使用其他設定。 若要了解所有對等互連設定，請閱讀[管理虛擬網路對等互連](virtual-network-manage-peering.md#create-peering)。
15. 按一下上一個步驟中的 [確定] 後，[新增對等互連] 刀鋒視窗隨即關閉，而且您會再次看到 [myVnet1 - 對等互連] 刀鋒視窗。 幾秒之後，您建立的對等互連會出現在刀鋒視窗中。 您建立之 **myVnet1ToMyVnet2** 對等互連的 [對等互連狀態] 資料行中列出 [已起始]。 您已將 Vnet1 對等互連到 Vnet2，但您現在必須將 Vnet2 對等互連到 Vnet1。 必須以建立雙線的對等互連，虛擬網路中的資源才能彼此通訊。
16. 再次針對 myVnet2 完成本節的步驟 10 - 14。  將對等互連命名為 *myVnet2ToMyVnet1* 並輸入 myVnet1 的 [資源識別碼]。
17. 按一下 [確定] 來建立 MyVnet2 的對等互連幾秒之後，您剛建立之 **myVnet2ToMyVnet1** 對等互連的 [對等互連狀態] 資料行中會列出 [已連線]。
18. 再次針對 MyVnet1 完成本節的步驟 10 - 11。 **myVnet1ToVNet2** 對等互連的 [對等互連狀態] 現在也是 [已連線]。 您在對等互連中兩個虛擬網路的 [對等互連狀態] 資料行中看到 [已連線] 之後，對等互連變已建立成功。
19. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
20. **選擇性︰**若要刪除您在本教學課程中建立的資源，請完成本文之[刪除資源](#delete-portal)一節中的步驟。

### <a name="different-subscriptions-different-deployment-models"></a>一個虛擬網路 (Resource Manager)、一個虛擬網路 (傳統)
 
[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-preview.md)]

1. 註冊預覽版。 在您註冊預覽版前，您無法對等互連透過存在於不同訂用帳戶中的不同部署模型建立的虛擬網路。 您無法在入口網站中，或使用 Azure CLI 註冊預覽版。 您只可以使用 PowerShell 註冊預覽版。 若要註冊預覽版，請完成下列工作：
    - 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
    - 若要從 Windows 電腦啟動 PowerShell 工作階段，請移至 [開始]，輸入 **powershell**，然後按一下 [PowerShell]。
    - 在 PowerShell 視窗中，輸入 `login-azurermaccount` 命令來登入 Azure。 您登入時使用的帳戶必須擁有必要的權限，才能建立虛擬網路對等互連。 如需詳細資訊，請參閱本文的[權限](#permissions)一節。
    - 針對兩個 Azure 訂用帳戶訂閱註冊預覽版功能，並從 PowerShell 輸入下列命令︰ 
    
    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    #
    ```
    在登入每個訂用帳戶時註冊預覽版。 在兩個訂用帳戶中您輸入下列命令後收到的 **RegistrationState** 輸出變成 [已註冊] 之前，請勿繼續進行下列步驟。

    ```powershell
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    #
    ```

2. 完成本文的[入口網站](#portal)一節中的步驟 1 - 7，但具有下列變更：
    - 在步驟 5 中為 myVnet2 選擇的訂用帳戶，不同於在步驟 4 中為 myVnet1 選擇的訂用帳戶 (與相同的 Azure Active Directory 租用戶相關聯)。
    - 在建立 myVnet2 時，於步驟 3 中選擇 [傳統]。
3. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myResourceGroup*。 當搜尋結果中出現 **MyResourceGroup** 時，按一下該項目。 **myresourcegroup** 資源群組的刀鋒視窗隨即出現。 資源群組包含在前述步驟中建立的兩個虛擬網路。
4. 按一下 [myVNet1]。
5. 完成本文的[透過 Resource Manager 建立的兩個虛擬網路](#different-subscriptions-same-deployment-model)一節中的步驟 2-9。
6. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myResourceGroup*。 當搜尋結果中出現 **MyResourceGroup** 時，按一下該項目。 **myresourcegroup** 資源群組的刀鋒視窗隨即出現。 資源群組包含在前述步驟中建立的兩個虛擬網路。
7. 按一下 [myVNet1]。
8. 在顯示的 [myVnet1] 刀鋒視窗，從刀鋒視窗左側的垂直選項清單中按一下 [對等互連]。
9. 在顯示的 [myVnet1 - 對等互連] 刀鋒視窗中，按一下 [+ 新增]
10. 在顯示的 [新增對等互連] 刀鋒視窗中，輸入或選取下列選項，然後按一下 [確定]：
     - **名稱**：*myVnet1ToMyVnet2*
     - **虛擬網路部署模型**︰選取 [傳統]。 
     - **我知道我的資源識別碼**：核取此方塊。
     - **資源識別碼**： 輸入 myVnet2 的資源識別碼。 只有核取 [我知道我的資源識別碼] 核取方塊，才會出現此方塊。
     - **允許虛擬網路存取**：確定已選取 [啟用]。
    本教學課程中不會使用其他設定。 若要了解所有對等互連設定，請閱讀[管理虛擬網路對等互連](virtual-network-manage-peering.md#create-peering)。

    建立虛擬網路 (Resource Manager) 與虛擬網路 (傳統) 之間的虛擬網路對等互連時，您只要設定從虛擬網路 (Resource Manager) 至虛擬網路 (傳統) 的對等互連。 建立虛擬網路 (Resource Manager) 的對等互連之後，其對等狀態為 [更新中]。 狀態會在幾秒後自動變更為 [已連線]，因為您不需要建立虛擬網路 (傳統) 的對等互連。 
11. **選擇性**：雖然本教學課程未涵蓋建立虛擬機器，但您可以在每個虛擬網路中建立一部虛擬機器，並從一部虛擬機器連線至另一部來驗證連線。
12. **選擇性︰**若要刪除您在本教學課程中建立的資源，請完成本文之[刪除資源](#delete-portal)一節中的步驟。

> [!NOTE]
> 雖然未涵蓋在本教學課程中，但您可以在本文的 [Azure CLI](#cli) 和 [PowerShell](#powershell) 章節中調整指令碼，以反映這本節涵蓋的差異。

## <a name="permissions"></a>權限

您用於建立虛擬網路對等互連的帳戶必須具有必要的角色或權限。 例如，如果您要將兩個虛擬網路 (分別名為 VNet1 和 VNet2) 對等互連，您的帳戶必須針對每個虛擬網路獲得下列最基本的角色或權限：
    
|虛擬網路|部署模型|角色|權限|
|---|---|---|---|
|VNet1|Resource Manager|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |傳統|[傳統網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|VNet2|Resource Manager|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||傳統|[傳統網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

深入了解[內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及如何對[自訂角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)指派特定權限 (僅限 Resource Manager)。

## <a name="delete"></a>刪除資源
當您完成本教學課程時，您可能會想刪除您在教學課程中建立的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。

### <a name="delete-portal"></a>Azure 入口網站

1. 在入口網站的搜尋方塊中，輸入 **myResourceGroup**。 在搜尋結果中按一下 [myResourceGroup]。
2. 在 [myResourceGroup] 刀鋒視窗中，按一下 [刪除] 圖示。
3. 若要確認刪除動作，請在 [輸入資源群組名稱] 方塊中輸入 **myResourceGroup**，然後按一下 [刪除]。

### <a name="delete-cli"></a>Azure CLI

從 Linux、macOS 或 Windows 命令殼層，輸入下列命令。 此外，按一下下列程式碼區塊右上方的 [試用] 按鈕，即可啟動 Cloud Shell。 然後，使用 [複製] 按鈕來複製範例程式碼並貼到 Cloud Shell 中。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

在 PowerShell 命令提示字元中，輸入下列命令︰

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

- 請先徹底熟悉重要[虛擬網路對等互連的條件約束和行為](virtual-network-manage-peering.md#about-peering)，再建立虛擬網路對等互連以供生產環境使用。
- 了解所有[虛擬網路對等互連設定](virtual-network-manage-peering.md#create-peering)。
- 了解如何透過虛擬網路對等互連來[建立中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。

