<properties
	pageTitle="使用 Azure CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager"
	description="本文詳述支援移轉服務功能使用 Azure CLI 將 Service Management 移轉至 Azure Resource Manager 的平台"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# 使用 Azure CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager

以下步驟說明如何使用 Azure CLI 命令，將 IaaS 資源從傳統移轉至 Resource Manager。本文需要 [Azure CLI](../xplat-cli-install.md) 登入 (`azure login`)。

## 步驟 1︰為移轉做準備

以下是建議您在評估從傳統移轉 IaaS 資源至 Resource Manager 的一些最佳作法

- 閱讀[這裡](virtual-machines-windows-migration-classic-resource-manager.md)查看不支援的組態或功能。如果您的虛擬機器使用不支援的組態或功能，建議您等到宣布支援該功能/組態再進行移轉。或者，您可以移除該功能或移出該組態，以利移轉進行 (如果這麼做符合您的需求)。
-	如果您是用自動化的指令碼部署基礎結構和應用程式。請嘗試使用這些移轉用指令碼建立類似的測試設定。或者，您也可以使用 Azure 入口網站設定範例環境。
- 因為服務目前是公開預覽版本，請確定您用來移轉的測試環境與您的生產環境隔離。請勿在測試和生產環境之間混用儲存體帳戶、VNET 或其他資源。

## 步驟 2︰設定您的訂用帳戶並註冊移轉公開預覽版本

因為移轉案例的緣故，您需要設定您的環境可使用傳統和 Resource Manager。[安裝 Azure CLI](../xplat-cli-install.md) 並[選取您的訂用帳戶](../xplat-cli-connect.md)。

使用下列命令來選取您的 Azure 訂用帳戶

	azure account set "azure-subscription-name"

使用下列命令註冊公開預覽版本。請注意，在某些情況下此命令會逾時，不過註冊會成功。遵循以下步驟來檢查註冊的狀態。

	azure provider register Microsoft.ClassicInfrastructureMigrate

等 5 分鐘讓註冊完成。您可以使用下列命令來檢查核准的狀態。務必確定 RegistrationState 是 'Registered' 後再繼續下一步。

	azure provider show Microsoft.ClassicInfrastructureMigrate

現在切換 CLI 至 asm 模式

	azure config mode asm

## 步驟 3︰移轉 IaaS 資源的命令

>[AZURE.NOTE] 請注意，下述所有作業都是等冪。如果您遇到不支援的功能或設定錯誤以外的任何情況，建議您再試一次準備、中止或認可作業，平台將會重試一次動作。

### 移轉雲端服務中的虛擬機器 (不是在虛擬網路中)

使用下列命令取得雲端服務清單，選擇您想要移轉的雲端服務。請注意，如果雲端服務中的 VM 是在 VNET 中，或是具有 Web/背景工作角色，您會收到錯誤訊息。

	azure service list

執行下列命令可從詳細資訊輸出中獲得雲端服務的部署名稱。在大部分情況下，部署名稱和雲端服務名稱相同。

	azure service show servicename -vv

為雲端服務中的虛擬機器做移轉的準備您有兩個選擇。

如果您想要將 VM 移轉至建立虛擬網路的平台上，請使用下列命令。

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

如果您想要移轉至 Resource Manager 部署模型中的現有虛擬網路，請使用下列命令。

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

一旦準備作業成功，則可以瀏覽詳細資訊輸出取得 VM 的移轉狀態，並確保 VM 處於「備妥」狀態。

	azure vm show vmName -vv

使用 CLI 或 Azure 入口網站檢查已備妥之資源的組態。如果尚未準備好進行移轉，您想要回到舊的狀態，請使用下列命令。

	azure service deployment abort-migration serviceName deploymentName

如果備妥的組態看起來沒問題，可以繼續進行，使用下列命令認可資源。

	azure service deployment commit-migration serviceName deploymentName

### 移轉虛擬網路中的虛擬機器

選取您要移轉的虛擬網路。請注意，虛擬網路是否包含 Web/背景工作角色，以及 VM 是否有不支援的組態，若有，您將收到驗證錯誤訊息。

使用下列命令取得訂用帳戶中的所有虛擬網路。

	azure network vnet list

使用下列命令為您選擇要移轉的虛擬網路做準備。

	azure network vnet prepare-migration virtualnetworkname

使用 CLI 或 Azure 入口網站檢查已備妥之虛擬機器的組態。如果尚未準備好進行移轉，您想要回到舊的狀態，請使用下列命令。

	azure network vnet abort-migration virtualnetworkname

如果備妥的組態看起來沒問題，可以繼續進行，使用下列命令認可資源。

	azure network vnet commit-migration virtualnetworkname

## 參考

- [支援將 IaaS 資源從傳統移轉至 Resource Manager 的平台](virtual-machines-windows-migration-classic-resource-manager.md)
- [深入技術探討支援從傳統移轉至 Resource Manager 的平台](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0511_2016-->