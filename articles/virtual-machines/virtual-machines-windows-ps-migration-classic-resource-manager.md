<properties
	pageTitle="使用 Azure PowerShell 將 IaaS 資源從傳統移轉至 Azure Resource Manager"
	description="本文詳述支援移轉服務功能使用 PowerShell 指令碼將 Service Management 移轉至 Azure Resource Manager 的平台"
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

以下步驟說明如何使用 Azure PowerShell 命令，將 IaaS 資源從傳統移轉至 Resource Manager。這些步驟遵循填空方法來移轉您的自訂環境。您只需要使用命令並將變數(以「$」為開頭的行) 取代為您自己的值。

## 步驟 1︰為移轉做準備

以下是建議您在評估從傳統移轉 IaaS 資源至 Resource Manager 的一些最佳作法

- 閱讀[這裡](virtual-machines-windows-migration-classic-resource-manager.md)查看不支援的組態或功能。如果您的虛擬機器使用不支援的組態或功能，建議您等到宣布支援該功能/組態再進行移轉。或者，您可以移除該功能或移出該組態，以利移轉進行 (如果這麼做符合您的需求)。
-	如果您是用自動化的指令碼部署基礎結構和應用程式。請嘗試使用這些移轉用指令碼建立類似的測試設定。或者，您也可以使用 Azure 入口網站設定範例環境。
- 因為服務目前是公開預覽版本，請確定您用來移轉的測試環境與您的生產環境隔離。請勿在測試和生產環境之間混用儲存體帳戶、VNET 或其他資源。

## 步驟 2：安裝最新的 Azure PowerShell

有兩個主要的安裝選項：[PowerShell 資源庫](https://www.powershellgallery.com/profiles/azure-sdk/)和 [WebPI](http://aka.ms/webpi-azps)。WebPI 每個月都會更新。PowerShell 資源庫將持續更新。

如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/)。

## 步驟 3︰設定您的訂用帳戶並註冊移轉公開預覽版本

首先，開啟 PowerShell 提示字元。因為移轉案例的緣故，您需要設定您的環境可使用傳統和 Resource Manager。

登入您的 Resource Manager 模型的帳戶。

	Login-AzureRmAccount

使用下列命令，可以取得可用的訂用帳戶。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

設定目前工作階段的 Azure 訂用帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

使用下列命令註冊公開預覽版本。

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

等 5 分鐘讓註冊完成。您可以使用下列命令來檢查核准的狀態。務必確定 RegistrationState 是 'Registered' 後再繼續下一步。

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

現在，登入您的傳統模型的帳戶。

	Add-AzureAccount

使用下列命令，可以取得可用的訂用帳戶。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

設定目前工作階段的 Azure 訂用帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## 步驟 4︰移轉 IaaS 資源的命令

>[AZURE.NOTE] 請注意，下述所有作業都是等冪。如果您遇到不支援的功能或設定錯誤以外的任何情況，建議您再試一次準備、中止或認可作業，平台將會重試一次動作。

### 移轉雲端服務中的虛擬機器 (不是在虛擬網路中)

使用下列命令取得雲端服務清單，選擇您想要移轉的雲端服務。請注意，如果雲端服務中的 VM 是在 VNET 中，或是具有 Web/背景工作角色，您會收到錯誤訊息。

	Get-AzureService | ft Servicename

使用下列命令取得雲端服務的部署名稱

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

為雲端服務中的虛擬機器做移轉的準備您有兩個選擇。

如果您想要將 VM 移轉至建立虛擬網路的平台上，請使用下列命令。

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

如果您想要移轉至 Resource Manager 部署模型中的現有虛擬網路，請使用下列命令。

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

一旦準備作業成功，則可以查詢 VM 的移轉狀態，並確保 VM 處於「備妥」狀態。

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

使用 PowerShell 或 Azure 入口網站檢查已備妥之資源的組態。如果尚未準備好進行移轉，您想要回到舊的狀態，請使用下列命令。

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

如果備妥的組態看起來沒問題，可以繼續進行，使用下列命令認可資源。

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### 移轉虛擬網路中的虛擬機器

選取您要移轉的虛擬網路。請注意，虛擬網路是否包含 Web/背景工作角色，以及 VM 是否有不支援的組態，若有，您將收到驗證錯誤訊息。

使用下列命令為要移轉的虛擬網路做準備。

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

使用 PowerShell 或 Azure 入口網站檢查已備妥之虛擬機器的組態。如果尚未準備好進行移轉，您想要回到舊的狀態，請使用下列命令。

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

如果備妥的組態看起來沒問題，可以繼續進行，使用下列命令認可資源。

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## 參考

- [支援將 IaaS 資源從傳統移轉至 Resource Manager 的平台](virtual-machines-windows-migration-classic-resource-manager.md)
- [深入技術探討支援從傳統移轉至 Resource Manager 的平台](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [利用 Community PowerShell 指令碼複製傳統虛擬機器至 Azure Resource Manager](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->