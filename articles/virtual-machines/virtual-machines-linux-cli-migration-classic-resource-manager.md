<properties
	pageTitle="使用 Azure CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager | Microsoft Azure"
	description="這篇文章提供使用 Azure CLI 進行平台支援之資源移轉 (從傳統移轉至 Azure Resource Manager) 的逐步解說"
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

以下步驟說明如何使用 Azure 命令列介面 (CLI) 命令，將基礎結構即服務 (IaaS) 資源從傳統部署模型移轉至 Azure Resource Manager 部署模型。本文需要 [Azure CLI](../xplat-cli-install.md) 登入 (`azure login`)。

## 步驟 1︰為移轉做準備

以下是您評估將 IaaS 資源從傳統移轉至 Resource Manager 時，我們所建議的一些最佳做法：

- 將[不支援的組態或功能清單](virtual-machines-windows-migration-classic-resource-manager.md)看一遍。如果您的虛擬機器使用不支援的組態或功能，建議您等到宣布支援該功能/組態之後，再進行移轉。或者，您可以移除該功能或移出該組態，以利移轉進行 (如果這麼做符合您的需求)。
-	如果您是使用自動化指令碼來部署現今的基礎結構和應用程式，請使用這些指令碼來嘗試建立相似的測試設定以進行移轉。或者，您也可以使用 Azure 入口網站來設定範例環境。

## 步驟 2︰設定您的訂用帳戶並註冊以進行移轉

針對移轉案例，您必須為傳統和 Resource Manager 模型設定您的環境。[安裝 Azure CLI](../xplat-cli-install.md) 並[選取您的訂用帳戶](../xplat-cli-connect.md)。

使用下列命令來選取 Azure 訂用帳戶。

	azure account set "azure-subscription-name"

>[AZURE.NOTE] 註冊是一次性步驟，但必須在嘗試移轉之前完成。如果不註冊，您會看到下列錯誤訊息

>	*BadRequest : Subscription is not registered for migration.* 

請使用下列命令向移轉資源提供者註冊。請注意，在某些情況下，此命令會逾時。不過，註冊將會成功。

	azure provider register Microsoft.ClassicInfrastructureMigrate

請等候 5 分鐘讓註冊完成。您可以使用下列命令來檢查核准狀態。請先確定 RegistrationState 是 `Registered`，再繼續進行。

	azure provider show Microsoft.ClassicInfrastructureMigrate

現在，請將 CLI 切換至 `asm` 模式。

	azure config mode asm

## 步驟 3︰執行命令來移轉 IaaS 資源

>[AZURE.NOTE] 下述所有作業都是等冪的。如果您有不支援的功能或組態錯誤以外的任何問題，建議您重新嘗試準備、中止或認可作業。如此，平台就會重新嘗試該動作。

### 移轉雲端服務中的虛擬機器 (不在虛擬網路中)

使用下列命令來取得雲端服務清單，然後選擇您想要移轉的雲端服務。請注意，如果雲端服務中的 VM 是在虛擬網路中，或是具有 Web/背景工作角色，您將會收到錯誤訊息。

	azure service list

執行下列命令，以從詳細資訊輸出中獲得雲端服務的部署名稱。在大部分情況下，部署名稱和雲端服務名稱相同。

	azure service show servicename -vv

準備好雲端服務中的虛擬機器以進行移轉。有兩個選項可供您選擇。

如果您想要將 VM 移轉至平台建立的虛擬網路，請使用下列命令。

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

如果您想要移轉至 Resource Manager 部署模型中的現有虛擬網路，請使用下列命令。

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

準備作業成功之後，您可以瀏覽詳細資訊輸出，以取得 VM 的移轉狀態並確保 VM 處於 `Prepared` 狀態。

	azure vm show vmName -vv

使用 CLI 或 Azure 入口網站來檢查已備妥之資源的組態。如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

	azure service deployment abort-migration serviceName deploymentName

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

	azure service deployment commit-migration serviceName deploymentName

### 移轉虛擬網路中的虛擬機器

選取您想要移轉的虛擬網路。請注意，如果虛擬網路包含 Web/背景工作角色，或有具備不支援之組態的 VM，您將會收到驗證錯誤訊息。

使用下列命令來取得訂用帳戶中的所有虛擬網路。

	azure network vnet list

使用下列命令來準備您所選擇的虛擬網路以進行移轉。

	azure network vnet prepare-migration virtualnetworkname

使用 CLI 或 Azure 入口網站來檢查已備妥之虛擬機器的組態。如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

	azure network vnet abort-migration virtualnetworkname

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

	azure network vnet commit-migration virtualnetworkname

### 移轉儲存體帳戶

完成虛擬機器移轉之後，我們建議您將移轉儲存體帳戶。

使用下列命令來準備儲存體帳戶以進行移轉

	azure storage account prepare-migration storageaccountname

使用 CLI 或 Azure 入口網站來檢查已備妥之儲存體帳戶的設定。如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

	azure storage account abort-migration storageaccountname

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

	azure storage account commit-migration storageaccountname

## 後續步驟

- [平台支援的 IaaS 資源移轉 (從傳統移轉至 Resource Manager)](virtual-machines-windows-migration-classic-resource-manager.md)
- [平台支援的從傳統移轉至 Resource Manager 的技術深入探討](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0706_2016-->