---
title: "建立 Azure 虛擬網路 | Microsoft Docs"
description: "了解如何建立有多個子網路的虛擬網路。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: zh-tw
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>建立有多個子網路的虛擬網路

在本教學課程中，您將了解如何建立具有不同的公用和私人子網路的基本 Azure 虛擬網路 (VNet)。 您可以將虛擬機器 (VM)、App Service 環境、虛擬機器擴展集、HDInsight 及雲端服務等 Azure 資源連線至子網路。 連線至 VNet 的資源可透過 Azure 私人網路彼此通訊。

以下各節所包含的步驟，會適用於使用 Azure [入口網站](#portal)、Azure [命令列介面](#cli) (CLI)、Azure [PowerShell](#powershell) 和 Azure Resource Manager [範本](#template)來部署 VNet。 無論您選擇使用哪一種工具來部署 VNet，結果都會相同。 按一下上述任何工具的連結，您便會直接前往本文中說明該工具的章節。 若要深入了解所有 VNet 和子網路的設定，請閱讀[管理 VNet](virtual-network-manage-network.md) 和[管理子網路](virtual-network-manage-subnet.md)文章。

## <a name="portal"></a>Azure 入口網站

1. 從網際網路瀏覽器開啟 Azure [入口網站](https://portal.azure.com)，並以您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
2. 在入口網站中按一下 [+ 新增] > [網路] > [虛擬網路]。
3. 在出現的 [虛擬網路] 刀鋒視窗中，讓 [選取部署模型] 底下的 [Resource Manager] 保持選取狀態，然後按一下 [建立]。
4. 在出現的 [建立虛擬網路] 刀鋒視窗中輸入下列值，然後按一下 [建立] 按鈕：

    |設定|值|
    |---|---|
    |名稱|*MyVnet*|
    |位址空間|*10.0.0.0/16*|
    |子網路名稱|公開|
    |子網路位址範圍|*10.0.0.0/24*|
    |資源群組|讓 [新建] 保持選取狀態，並輸入 MyResourceGroup**|
    |訂用帳戶和位置|選取您的訂用帳戶和位置。

    如果您不熟悉 Azure，請深入了解[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions) (這也稱為區域)。
6. 入口網站只能讓您在建立 VNet 時建立一個子網路。 本教學課程會在 VNet 建立好之後，接著建立第二個子網路。 您稍後可以將可透過網際網路存取的資源連線至「公用」子網路，並將無法從網際網路存取的資源連線至私人子網路。 若要建立第二個子網路，請在入口網站頂端的 [搜尋資源] 方塊中輸入「MyVnet」。 當搜尋結果中出現 **MyVnet** 時，按一下該項目。 如果您的訂用帳戶中有多個同名的 VNet，您會看到每個同名 VNet 底下都列出了資源群組名稱。 請確定您點按的 MyVnet 結果底下是 MyResourceGroup。
7. 在出現的 [MyVnet] 刀鋒視窗中，按一下 [設定] 底下的 [子網路]。
8. 在 [MyVnet - 子網路] 刀鋒視窗中，按一下 [+子網路]。
9. 在 [新增子網路] 刀鋒視窗中輸入「私人」作為 [名稱]，並輸入「10.0.1.0/24」作為 [位址範圍]，然後按一下 [確定]。
10. 在 [MyVnet - 子網路] 刀鋒視窗中檢閱子網路。 您會看到您所建立的**公用**和**私人**子網路。
11. **選擇性︰**若要刪除本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-portal)一節的步驟。

## <a name="cli"></a>CLI
雖然從 Windows、Linux 或 macOS 執行的 CLI 命令都相同，但不同作業系統殼層的編碼方式會有一些差異。 下列指示適用於執行內含 CLI 命令的 Bash 指令碼︰

1. 從網際網路瀏覽器開啟 Azure [入口網站](https://portal.azure.com)，並以您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
2. 在入口網站頂端的 [搜尋資源] 列右邊，按一下 [>_] 圖示以啟動 Bash Azure Cloud Shell (預覽版)。 [Cloud Shell] 窗格會出現在入口網站底部，並在幾秒後顯示 **username@Azure:~$** 提示字元。 Cloud Shell 會使用您向入口網站驗證時所使用的認證，自動將您登入 Azure。
3. 在瀏覽器中複製下列指令碼：
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. 建立指令碼檔案並加以儲存。 從 Cloud Shell 提示字元輸入 `nano myscript.sh --nonewlines`。 此命令會啟動 GNU nano 編輯器，並開啟空白的 myscript.sh 檔案。 將滑鼠游標放在編輯器視窗內、按一下滑鼠右鍵，然後按一下 [貼上]。 系統不會跨工作階段保存 Cloud Shell 儲存體。 如果您想要跨 Cloud Shell 工作階段來保存指令碼，請為 Cloud Shell 設定[永續性儲存體](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 
5. 在鍵盤上按住 **Ctrl+X** 鍵，輸入 **Y**，然後按 **Enter** 鍵將檔案儲存為 myscript.sh。
6. 從 Cloud Shell 提示字元使用 `chmod +x myscript.sh` 命令，將檔案標示為可執行檔。
7. 輸入 `./myscript.sh` 來執行指令碼。
8. 指令碼完成之後，請在 Bash Cloud Shell 中複製並貼上下列命令，以檢閱 VNet 的子網路︰
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **選擇性︰**若要刪除本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-cli)一節的步驟。

## <a name="powershell"></a>PowerShell
1. 安裝最新版的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請閱讀 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 按一下 Windows 的 [開始] 按鈕，輸入 **Powershell**，然後按一下搜尋結果中的 [PowerShell]，以啟動 PowerShell 工作階段。
3. 在 PowerShell 視窗中輸入 `login-azurermaccount` 命令，以使用您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
4. 在瀏覽器中複製下列指令碼：
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. 若要執行指令碼，請在 PowerShell 視窗中按一下滑鼠右鍵。
6. 在 PowerShell 視窗中複製並貼上命令，以檢閱 VNet 的子網路︰
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **選擇性︰**若要刪除本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-powershell)一節的步驟。

## <a name="template"></a>範本

您可以使用 Azure Resource Manager 範本來部署 VNet。 若要深入了解範本，請閱讀[什麼是 Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment) 一文。 若要存取範本並了解它的參數，請看[建立有兩個子網路之 VNet 的範本](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)網頁。 您可以使用[入口網站](#template-portal)、[CLI](#template-cli) 或 [PowerShell](#template-powershell) 來部署範本。

**選擇性︰**若要刪除本教學課程中所建立的資源，請完成本文之[刪除資源](#delete)一節中任何小節的步驟。

### <a name="template-portal"></a>入口網站

1. 在瀏覽器中開啟[網頁](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)範本。
2. 按一下 [部署至 Azure] 按鈕，以開啟 Azure 入口網站登入頁面。
3. 使用您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入入口網站。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
4. 為各個參數輸入下列值︰

    |參數|值|
    |---|---|
    |訂用帳戶|選取您的訂用帳戶。|
    |資源群組|MyResourceGroup|
    |位置|選取位置。|
    |Vnet 名稱|MyVnet|
    |Vnet 位址首碼|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|公開|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|私人|

5. 同意條款及條件，然後按一下 [購買] 以部署 VNet。

### <a name="template-cli"></a>CLI

1. 從網際網路瀏覽器開啟 Azure [入口網站](https://portal.azure.com)，並以您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
2. 在入口網站頂端的 [搜尋資源] 列右邊，按一下 [>_] 圖示以啟動 Bash Azure Cloud Shell (預覽版)。 [Cloud Shell] 窗格會出現在入口網站底部，並在幾秒後顯示 **username@Azure:~$** 提示字元。 Cloud Shell 會使用您向入口網站驗證時所使用的認證，自動將您登入 Azure。
3. 輸入下列命令來建立 VNet 的資源群組︰ `az group create --name MyResourceGroup --location eastus`
4. 您可以使用下列值來部署範本︰
    - **預設參數值︰**輸入下列命令︰  `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **自訂參數值︰**下載並修改範本再加以部署、從命令列使用參數來部署範本，或是使用不同的參數檔來部署範本。 您可以按一下[建立有兩個子網路之 VNet 的範本](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)網頁上的 [瀏覽 GitHub] 按鈕，來下載範本和參數檔。 在 GitHub 中，按一下 **azuredeploy.parameters.json** 或 **azuredeploy.json** 檔案，然後按一下該檔案的 [Raw] 按鈕。 在瀏覽器中複製內容，並將它儲存到您電腦上的檔案。 修改範本中的參數值，或使用不同的參數檔來部署範本。  

    若要深入了解如何使用這些方法來部署範本，請輸入 `az group deployment create --help`。

### <a name="template-powershell"></a>PowerShell

1. 安裝最新版的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請閱讀 [Azure PowerShell 概觀](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 按一下 Windows 的 [開始] 按鈕，輸入 **powershell**，然後按一下搜尋結果中的 [PowerShell]，以啟動 PowerShell 工作階段。
3. 在 PowerShell 視窗中輸入 `login-azurermaccount` 命令，以使用您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
4. 輸入下列命令來建立 VNet 的資源群組︰ `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. 您可以使用下列值來部署範本︰
    - **預設參數值︰**若要這麼做，請輸入下列命令︰  `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **自訂參數值︰**若要這麼做，您可以下載並修改範本再加以部署、從命令列使用參數來部署範本，或是使用不同的參數檔來部署範本。 您可以按一下[建立有兩個子網路之 VNet 的範本](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)網頁上的 [瀏覽 GitHub] 按鈕，來下載範本和參數檔。 在 GitHub 中，按一下 **azuredeploy.parameters.json** 或 **azuredeploy.json** 檔案，然後按一下該檔案的 [Raw] 按鈕。 在瀏覽器中複製內容，並將它儲存到您電腦上的檔案。 修改範本中的參數值，或使用不同的參數檔來部署範本。  

    若要深入了解如何使用這些方法來部署範本，請輸入 `Get-Help New-AzureRmResourceGroupDeployment`。 

## <a name="delete"></a>刪除資源
在完成本教學課程之後，建議您刪除資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。

### <a name="delete-portal"></a>入口網站

1. 在入口網站中，請於入口網站頂端的 [搜尋資源] 方塊中開始輸入「MyResourceGroup」。 當搜尋結果中出現 **MyResourceGroup** 時，按一下該項目。
2. 在出現的 [MyResourceGroup] 刀鋒視窗中，按一下刀鋒視窗頂端附近的 [刪除] 圖示。
3. 若要確認刪除動作，請在 [輸入資源群組名稱:] 方塊中輸入「MyResourceGroup」，然後按一下 [刪除]。

### <a name="delete-cli"></a>CLI

從 Cloud Shell 提示字元中，輸入下列命令︰`az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

從 PowerShell 提示字元中，輸入下列命令︰`Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>後續步驟

- 若要了解所有 VNet 和子網路的設定，請閱讀[管理 VNet](virtual-network-manage-network.md#view-vnet) 和[管理子網路](virtual-network-manage-subnet.md#create-subnet)文章。 我們提供了各種選項供您建立生產環境的 VNet 和子網路，以符合您的不同需求。
- 對子網路建立和套用[網路安全性群組](virtual-networks-nsg.md) (NSG)，以篩選輸入和輸出的子網路流量。
- 建立 [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM，並將它連線至 VNet。
- 使用 [VNet 對等互連](virtual-network-peering-overview.md)將某個 VNet 連線到相同位置中的另一個 VNet。
- 使用[網站對網站虛擬私人網路](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (VPN) 或 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 線路，將 VNet 連線至內部部署網路。

