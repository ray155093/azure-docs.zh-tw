<properties
	pageTitle="使用 Azure PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager | Microsoft Azure"
	description="這篇文章提供使用 PowerShell 指令碼進行平台支援之資源移轉 (從傳統移轉至 Azure Resource Manager) 的逐步解說"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# 使用 Azure PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager

以下步驟說明如何使用 Azure PowerShell 命令，將基礎結構即服務 (IaaS) 資源從傳統部署模型移轉至 Azure Resource Manager 部署模型。這些步驟遵循填空方法來移轉您的自訂環境。您只需要採用命令並將變數 (開頭為 "$" 的行) 取代為您自己的值即可。

## 步驟 1︰為移轉做準備

以下是您評估將 IaaS 資源從傳統移轉至 Resource Manager 時，我們所建議的一些最佳做法：

- 將[不支援的組態或功能清單](virtual-machines-windows-migration-classic-resource-manager.md)看一遍。如果您的虛擬機器使用不支援的組態或功能，建議您等到宣布支援該組態/功能之後，再進行移轉。或者，您可以移除該功能或移出該組態，以利移轉進行 (如果這麼做符合您的需求)。
-	如果您是使用自動化指令碼來部署現今的基礎結構和應用程式，請使用這些指令碼來嘗試建立相似的測試設定以進行移轉。或者，您也可以使用 Azure 入口網站來設定範例環境。

## 步驟 2：安裝最新版的 Azure PowerShell

您有下列兩個主要的安裝選項：[PowerShell 資源庫](https://www.powershellgallery.com/profiles/azure-sdk/)和 [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps)。WebPI 每個月都會更新。PowerShell 資源庫將持續更新。

如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/)。

## 步驟 3︰設定您的訂用帳戶並註冊以進行移轉

首先，開啟 PowerShell 提示字元。針對移轉案例，您必須為傳統和 Resource Manager 模型設定您的環境。

登入您的 Resource Manager 模型帳戶。

	Login-AzureRmAccount

使用下列命令，可以取得可用的訂用帳戶。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

設定目前工作階段的 Azure 訂用帳戶。以正確的名稱取代括號中 (包括 < 和 > 字元) 的所有內容。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

>[AZURE.NOTE] 註冊是一次性步驟，但必須在嘗試移轉之前完成。如果不註冊，您會看到下列錯誤訊息

>	*BadRequest : Subscription is not registered for migration.* 

請使用下列命令向移轉資源提供者註冊。
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

請等候 5 分鐘讓註冊完成。您可以使用下列命令來檢查核准狀態。請先確定 RegistrationState 是 `Registered`，再繼續進行。

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

現在，登入您的傳統模型帳戶。

	Add-AzureAccount

使用下列命令，可以取得可用的訂用帳戶。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

設定目前工作階段的 Azure 訂用帳戶。以正確的名稱取代括號中 (包括 < 和 > 字元) 的所有內容。

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## 步驟 4︰執行命令來移轉 IaaS 資源

>[AZURE.NOTE] 下述所有作業都是等冪的。如果您有不支援的功能或組態錯誤以外的任何問題，建議您重新嘗試準備、中止或認可作業。如此，平台就會重新嘗試該動作。

### 移轉雲端服務中的虛擬機器 (不在虛擬網路中)

使用下列命令來取得雲端服務清單，然後選擇您想要移轉的雲端服務。請注意，如果雲端服務中的 VM 是在虛擬網路中，或是具有 Web/背景工作角色，您將會收到錯誤訊息。

	Get-AzureService | ft Servicename

使用下列命令來取得雲端服務的部署名稱。

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

準備好雲端服務中的虛擬機器以進行移轉。有兩個選項可供您選擇。

如果您想要將 VM 移轉至平台建立的虛擬網路，請使用下列命令。

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

如果您想要移轉至 Resource Manager 部署模型中的現有虛擬網路，請使用下列命令。

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

準備作業成功之後，您可以查詢 VM 的移轉狀態並確保 VM 處於 `Prepared` 狀態。

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

使用 PowerShell 或 Azure 入口網站來檢查已備妥之資源的組態。如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### 移轉虛擬網路中的虛擬機器

選取您想要移轉的虛擬網路。請注意，如果虛擬網路包含 Web/背景工作角色，或有具備不支援之組態的 VM，您將會收到驗證錯誤訊息。

使用下列命令來準備虛擬網路以進行移轉。

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

使用 PowerShell 或 Azure 入口網站來檢查已備妥之虛擬機器的組態。如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### 移轉儲存體帳戶

完成虛擬機器移轉之後，我們建議您將移轉儲存體帳戶。

使用下列命令來準備儲存體帳戶以進行移轉

	$storageAccountName = "storagename"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

使用 PowerShell 或 Azure 入口網站來檢查已備妥之儲存體帳戶的組態。如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## 後續步驟

- [平台支援的 IaaS 資源移轉 (從傳統移轉至 Resource Manager)](virtual-machines-windows-migration-classic-resource-manager.md)
- [平台支援的從傳統移轉至 Resource Manager 的技術深入探討](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [使用社群 PowerShell 指令碼將傳統虛擬機器複製到 Azure Resource Manager](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0706_2016-->