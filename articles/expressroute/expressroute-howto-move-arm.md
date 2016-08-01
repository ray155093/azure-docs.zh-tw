<properties
   pageTitle="將 ExpressRoute 電路從傳統移至 Resource Manager | Microsoft Azure"
   description="本頁面會描述如何將傳統的電路移至 Resource Manager 部署模型。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="ganesr"/>


# 將 ExpressRoute 電路從傳統部署模型移至 Resource Manager 部署模型

## 組態必要條件

- 您需要最新版的 Azure PowerShell 模組 (至少 1.0 版)。
- 開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)和[工作流程](expressroute-workflows.md)。
- 更進一步之前，請先檢閱[將 ExpressRoute 電路從傳統移至 Resource Manager 部署模型](expressroute-move.md)下提供的資訊。請確定您已完全了解可能的限制。
- 如果您想要將 Azure ExpressRoute 電路從傳統部署模型移至 Azure Resource Manager 部署模型，您必須完全設定電路讓它能在傳統部署模型中運作。
- 請確定您擁有建立在 Resource Manager 部署模型中建立的資源群組。

## 將 ExpressRoute 電路移至 Resource Manager 部署模型

您必須將 ExpressRoute 電路移至資源管理員部署模型，以便可在傳統和 Resource Manager 兩種部署模型中使用它。您可以執行下列 PowerShell 命令執行這項作業。

### 步驟 1︰從傳統部署模型收集電路詳細資訊

您需要先收集 ExpressRoute 電路的相關資訊。

登入 Azure 傳統環境並收集服務金鑰。您可以使用下列 PowerShell 程式碼片段來收集資訊︰

	# Sign in to your Azure account
	Add-AzureAccount

	# Select the appropriate Azure subscription
	Select-AzureSubscription "<Enter Subscription Name here>"

	# Import the PowerShell modules for Azure and ExpressRoute
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

	# Get the service keys of all your ExpressRoute circuits
	Get-AzureDedicatedCircuit

複製電路的**服務金鑰**，這個電路就是您想要移至 Resource Manager 部署模型的電路。

### 步驟 2︰登入 Resource Manager 環境並建立新的資源群組

您可以使用下列程式碼片段建立新的資源群組：

	# Sign in to your Azure Resource Manager environment
	Login-AzureRmAccount

	# Select the appropriate Azure subscription
	Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

	#Create a new resource group if you don't already have one
	New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

如果已經有了資源群組，您也可以使用現有的資源群組。

### 步驟 3：將 ExpressRoute 電路移至 Resource Manager 部署模型

您已準備就緒，可將 ExpressRoute 電路從傳統部署模型移至 Resource Manager 部署模型。更進一步之前，請先檢閱[將 ExpressRoute 電路從傳統移至 Resource Manager 部署模型](expressroute-move.md)下提供的資訊。

您可以執行下列程式碼片來執行這項作業：

	Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] 移動完成之後，列在前一個 Cmdlet 中的新名稱會用來處理資源。電路基本上會重新命名。

## 為兩種部署模型啟用 ExpressRoute 電路

您必須先將 ExpressRoute 電路移至 Resource Manager 部署模型，再控制存取部署模型。

執行下列的 Cmdlet 以存取這兩種部署模型︰

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

成功完成這項作業後，您就可以在傳統部署模型中檢視電路。

執行下列動作以取得 ExpressRoute 電路的詳細資訊：

    get-azurededicatedcircuit

您必須能夠看到列出的服務金鑰。您現在可以使用命令管理連至 ExpressRoute 線路的連結，標準傳統部署模型命令用於傳統 VNET，標準 ARM 命令用於 ARM VNET。下列文件會引導您了解如何管理 ExpressRoute 電路的連結︰

- [在 Resource Manager 部署模型中將虛擬網路連結到 ExpressRoute 電路](expressroute-howto-linkvnet-arm.md)
- [在傳統部署模型中將虛擬網路連結到 ExpressRoute 電路](expressroute-howto-linkvnet-classic.md)


## 停用傳統部署模型的 ExpressRoute 電路

執行下列的 Cmdlet 停止存取傳統部署模型︰

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

成功完成這項作業後，您就不能在傳統部署模型中檢視電路。

## 後續步驟

建立好線路後，請務必執行下列作業：

- [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-arm.md)
- [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0720_2016-->