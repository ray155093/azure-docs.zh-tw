---
title: "建立具有子網路的 Azure 虛擬網路 | Microsoft Docs"
description: "了解如何在 Azure 中建立具有多個子網路的虛擬網路。"
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
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1ecfba0470c31d1119b5e6c0f9d8538abe40f834
ms.contentlocale: zh-tw
ms.lasthandoff: 06/02/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>建立有多個子網路的虛擬網路

本教學課程可供了解如何建立具有不同公用和私人子網路的基本 Azure 虛擬網路。 您可以將各種 Azure 資源 (如虛擬機器、Azure App Service 環境、虛擬機器擴展集、Azure HDInsight 及其他雲端服務) 連線至子網路。 連線至虛擬網路的資源可透過 Azure 私人網路彼此通訊。

您可以採取下列各節所包含的步驟，使用 [Azure 入口網站](#portal)、Azure 命令列介面 ([Azure CLI](#azure-cli))、[Azure PowerShell](#powershell) 和 [Azure Resource Manager 範本](#resource-manager-template)來部署虛擬網路。 無論您使用哪一種工具來部署虛擬網路，結果都會相同。 按一下工具連結即可前往教學課程中關於該工具的章節。 若要深入了解所有虛擬網路和子網路的設定，請參閱[管理虛擬網路](virtual-network-manage-network.md)和[管理虛擬子網路](virtual-network-manage-subnet.md)。

## <a name="portal"></a>Azure 入口網站

1. 在網際網路瀏覽器中，移至 [Azure 入口網站](https://portal.azure.com)。 使用 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)來登入。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在入口網站中按一下 [+新增] > [網路] > [虛擬網路]。
3. 在 [虛擬網路] 刀鋒視窗中，讓 [選取部署模型] 底下的 [Resource Manager] 保持選取狀態，然後按一下 [建立]。
4. 在 [建立虛擬網路] 刀鋒視窗中輸入下列值，然後按一下 [建立]：

    |設定|值|
    |---|---|
    |名稱|MyVnet|
    |位址空間|10.0.0.0/16|
    |子網路名稱|公開|
    |子網路位址範圍|10.0.0.0/24|
    |資源群組|讓 [新建] 保持選取狀態，然後輸入 **MyResourceGroup**。|
    |訂用帳戶和位置|選取您的訂用帳戶和位置。

    如果您不熟悉 Azure，請深入了解[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions) (也稱為「區域」)。
6. 當您在入口網站中建立虛擬網路時，您只能建立一個子網路。 在本教學課程中，您會在建立虛擬網路後建立第二個子網路。 稍後您可能會將可從網際網路存取的資源連線到**公用**子網路。 您也可能會將無法從網際網路存取的資源連線到**私人**子網路。 若要建立第二個子網路，請在頁面頂端的 [搜尋資源] 方塊中輸入 **MyVnet**。 在搜尋結果中按一下 [MyVnet]。 如果您的訂用帳戶中有多個同名虛擬網路，請檢查每個虛擬網路下面所列出的資源群組。 確定您所按的是擁有資源群組 **MyResourceGroup** 的 **MyVnet** 搜尋結果。
7. 在 [MyVnet] 刀鋒視窗中，按一下 [設定] 底下的 [子網路]。
8. 在 [MyVnet - 子網路] 刀鋒視窗中，按一下 [+子網路]。
9. 在 [新增子網路] 刀鋒視窗中，對 [名稱] 輸入**私人**。 對 [位址範圍] 輸入 **10.0.1.0/24**。  按一下 [確定] 。
10. 在 [MyVnet - 子網路] 刀鋒視窗中檢閱子網路。 您就會看到您所建立的**公用**和**私人**子網路。
11. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-portal)中的步驟。

## <a name="azure-cli"></a>Azure CLI

無論您是從 Windows、Linux 或 macOS 執行命令，所使用的 Azure CLI 命令都相同。 不過，不同作業系統殼層的指令碼編寫會有差異。 下列指示適用於執行具有 Azure CLI 命令的 Bash 指令碼︰

1. 在網際網路瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com)。 使用 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)來登入。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在入口網站頁面頂端的 [搜尋資源] 方塊右邊，按一下 [>_] 圖示以啟動 Bash Azure Cloud Shell (預覽版)。 入口網站底部隨即會出現 [Cloud Shell] 窗格。 幾秒鐘之後，畫面上便會出現 **username@Azure:~$** 提示字元。 Cloud Shell 會使用您用來登入入口網站的認證，自動將您登入 Azure。
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
    
    # Create an additional subnet within the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. 建立指令碼檔案並加以儲存。 在 Cloud Shell 命令提示字元輸入 `nano myscript.sh --nonewlines`。 此命令會啟動 GNU nano 編輯器，並開啟空白的 myscript.sh 檔案。 將游標放在編輯器視窗內、按一下滑鼠右鍵，然後按一下 [貼上]。  
5. 若要將檔案儲存為 myscript.sh，請按 Ctrl + X，輸入 **Y**，然後按 Enter 鍵。 Cloud Shell 儲存體不會跨工作階段保存已儲存的檔案。 如果您想要跨 Cloud Shell 工作階段來保存指令碼，請為 Cloud Shell 設定[永續性儲存體](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，並將檔案儲存到永續性儲存體。
6. 若要將檔案標示為可執行檔，請在 Cloud Shell 命令提示字元中執行 `chmod +x myscript.sh` 命令。
7. 若要執行指令碼，請輸入 `./myscript.sh`。
8. 當指令碼執行完成時，若要檢閱虛擬網路的子網路，請複製下列命令，並貼到 [Bash Cloud Shell] 窗格：
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-cli)中的步驟。

## <a name="powershell"></a>PowerShell

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 若要啟動 PowerShell 工作階段，請移至 [開始]，輸入 **powershell**，然後按一下 [PowerShell]。
3. 在 PowerShell 視窗中使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入，然後輸入 `login-azurermaccount`。
4. 在瀏覽器中複製下列指令碼：
    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. 若要執行指令碼，請在 PowerShell 視窗中按一下滑鼠右鍵。
6. 若要檢閱虛擬網路的子網路，請複製下列命令，然後貼到 PowerShell 視窗：
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete-powershell)中的步驟。

## <a name="resource-manager-template"></a>Resource Manager 範本

您可以使用 Azure Resource Manager 範本來部署虛擬網路。 若要深入了解範本，請參閱[什麼是 Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment)。 若要存取範本並了解其參數，請參閱[建立具有兩個子網路的虛擬網路](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)範本。 您可以使用[入口網站](#template-portal)、[Azure CLI](#template-cli) 或 [PowerShell](#template-powershell) 來部署範本。

**選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成本文之[刪除資源](#delete)中任何小節的步驟。

### <a name="template-portal"></a>Azure 入口網站

1. 在瀏覽器中開啟[範本頁面](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)。
2. 按一下 [部署至 Azure] 按鈕。 這會開啟 Azure 入口網站登入頁面。
3. 使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入入口網站。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。
4. 為各個參數輸入下列值︰

    |參數|值|
    |---|---|
    |訂用帳戶|選取您的訂用帳戶|
    |資源群組|MyResourceGroup|
    |位置|選取位置|
    |Vnet 名稱|MyVnet|
    |Vnet 位址首碼|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|公開|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|私人|

5. 同意條款及條件，然後按一下 [購買] 以部署虛擬網路。

### <a name="template-cli"></a>Azure CLI

1. 在[入口網站](https://portal.azure.com)中，使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)來登入。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。
2. 在 [搜尋資源] 方塊右邊，按一下 [>_] 圖示以啟動 Bash Azure Cloud Shell (預覽版)。 入口網站底部隨即會出現 [Cloud Shell] 窗格。 幾秒鐘之後，畫面上便會出現 **username@Azure:~$** 提示字元。 Cloud Shell 會使用您用來登入 Azure 入口網站的認證，自動將您登入 Azure。
3. 若要建立虛擬網路的資源群組，請輸入下列命令︰ `az group create --name MyResourceGroup --location eastus`
4. 您可以使用下列其中一個參數選項來部署範本：
    - **預設參數值**。 輸入下列命令：  `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **自訂參數值**。 請先下載並修改範本，再部署範本。 您也可以在命令列使用參數來部署範本，或使用不同的參數檔案來部署範本。 若要下載範本和參數檔，請按一下[建立具有兩個子網路的虛擬網路](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)範本頁面上的 [瀏覽 GitHub] 按鈕。 在 GitHub 中，按一下 **azuredeploy.parameters.json** 或 **azuredeploy.json** 檔案。 然後，按一下 [原始] 按鈕來顯示該檔案。 在瀏覽器中，複製該檔案的內容。 將內容儲存至您電腦上的檔案。 您可以修改範本中的參數值，或使用不同的參數檔來部署範本。  

    若要深入了解如何使用這些方法來部署範本，請輸入 `az group deployment create --help`。

### <a name="template-powershell"></a>PowerShell

1. 安裝最新版的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請參閱 [Azure PowerShell 概觀](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 若要啟動 PowerShell 工作階段，請移至 [開始]，輸入 **powershell**，然後按一下 [PowerShell]。
3. 在 PowerShell 視窗中使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入，然後輸入 `login-azurermaccount`。
4. 若要建立虛擬網路的資源群組，請輸入下列命令︰ `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. 您可以使用下列其中一個參數選項來部署範本：
    - **預設參數值**。 輸入下列命令：  `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **自訂參數值**。 請先下載並修改範本，再部署範本。 您也可以在命令列使用參數來部署範本，或使用不同的參數檔案來部署範本。 若要下載範本和參數檔，請按一下[建立具有兩個子網路的虛擬網路](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)範本頁面上的 [瀏覽 GitHub] 按鈕。 在 GitHub 中，按一下 **azuredeploy.parameters.json** 或 **azuredeploy.json** 檔案。 然後，按一下 [原始] 按鈕來顯示該檔案。 在瀏覽器中，複製該檔案的內容。 將內容儲存至您電腦上的檔案。 您可以修改範本中的參數值，或使用不同的參數檔來部署範本。  

    若要深入了解如何使用這些方法來部署範本，請輸入 `Get-Help New-AzureRmResourceGroupDeployment`。 

## <a name="delete"></a>刪除資源
當您完成本教學課程時，您可能會想刪除您在教學課程中使用的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。

### <a name="delete-portal"></a>Azure 入口網站

1. 在入口網站的搜尋方塊中，輸入 **MyResourceGroup**。 在搜尋結果中按一下 [MyResourceGroup]。
2. 在 [MyResourceGroup] 刀鋒視窗中，按一下 [刪除] 圖示。
3. 若要確認刪除動作，請在 [輸入資源群組名稱] 方塊中輸入 **MyResourceGroup**，然後按一下 [刪除]。

### <a name="delete-cli"></a>Azure CLI

在 Cloud Shell 命令提示字元中，輸入下列命令︰`az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

在 PowerShell 命令提示字元中，輸入下列命令︰`Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>後續步驟

- 若要了解所有虛擬網路和子網路的設定，請參閱[管理虛擬網路](virtual-network-manage-network.md#view-vnet)和[管理虛擬子網路](virtual-network-manage-subnet.md#create-subnet)。 您有各種選項可在生產環境中使用虛擬網路和子網路，以便符合不同的需求。
- 對子網路建立和套用[網路安全性群組](virtual-networks-nsg.md) (NSG)，以篩選輸入和輸出的子網路流量。
- 建立 [Windows 虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，然後將它連線至虛擬網路。
- 使用[虛擬網路對等互連](virtual-network-peering-overview.md)將虛擬網路連線到相同位置中的另一個虛擬網路。
- 使用[站台對站台虛擬私人網路](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 線路將虛擬網路連線至內部部署網路。

