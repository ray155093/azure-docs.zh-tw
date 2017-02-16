---
title: "使用 Azure CLI 將 VM 移轉至 Resource Manager | Microsoft Docs"
description: "這篇文章提供使用 Azure CLI 進行平台支援之資源移轉 (從傳統移轉至 Azure Resource Manager) 的逐步解說"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e64449991bc28427d8f559ed13c3bdf9160488db
ms.openlocfilehash: 92211cc98b6d8394ff04bc7c2fe33f7bd710713b


---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>使用 Azure CLI 將 IaaS 資源從傳統移轉至 Azure Resource Manager
以下步驟說明如何使用 Azure 命令列介面 (CLI) 命令，將基礎結構即服務 (IaaS) 資源從傳統部署模型移轉至 Azure Resource Manager 部署模型。 本文需要 [Azure CLI](../xplat-cli-install.md)。

> [!NOTE]
> 下述所有作業都是等冪的。 如果您有不支援的功能或組態錯誤以外的任何問題，建議您重新嘗試準備、中止或認可作業。 如此，平台就會重新嘗試該動作。
> 
> 

## <a name="step-1-prepare-for-migration"></a>步驟 1︰為移轉做準備
以下是您評估將 IaaS 資源從傳統移轉至 Resource Manager 時，我們所建議的一些最佳做法：

* 將 [不支援的組態或功能清單](virtual-machines-windows-migration-classic-resource-manager.md)看一遍。 如果您的虛擬機器使用不支援的組態或功能，建議您等到宣布支援該功能/組態之後，再進行移轉。 或者，您可以移除該功能或移出該組態，以利移轉進行 (如果這麼做符合您的需求)。
* 如果您是使用自動化指令碼來部署現今的基礎結構和應用程式，請使用這些指令碼來嘗試建立相似的測試設定以進行移轉。 或者，您也可以使用 Azure 入口網站來設定範例環境。

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>步驟 2︰設定您的訂用帳戶並註冊提供者
針對移轉案例，您必須為傳統和 Resource Manager 模型設定您的環境。 [安裝 Azure CLI](../xplat-cli-install.md) 並[選取您的訂用帳戶](../xplat-cli-connect.md)。

登入您的帳戶。

    azure login

使用下列命令來選取 Azure 訂用帳戶。

    azure account set "<azure-subscription-name>"

> [!NOTE]
> 註冊是一次性步驟，但必須在嘗試移轉之前完成。 如果不註冊，您會看到下列錯誤訊息 
> 
> *不正確的要求︰訂用帳戶未針對移轉進行註冊。* 
> 
> 

請使用下列命令向移轉資源提供者註冊。 請注意，在某些情況下，此命令會逾時。 不過，註冊將會成功。

    azure provider register Microsoft.ClassicInfrastructureMigrate

請等候&5; 分鐘讓註冊完成。 您可以使用下列命令來檢查核准狀態。 請先確定 RegistrationState 是 `Registered` ，再繼續進行。

    azure provider show Microsoft.ClassicInfrastructureMigrate

現在，請將 CLI 切換至 `asm` 模式。

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>步驟 3︰確定您目前的部署或 VNET 的 Azure 區域中有足夠的 Azure Resource Manager 虛擬機器核心
針對這個步驟，您將需要切換到 `arm` 模式。 請使用下列命令來執行此操作。

```
azure config mode arm
```

您可以使用下列 CLI 命令來檢查您目前在 Azure Resource Manager 中擁有的核心數量。 若要深入了解核心配額，請參閱 [限制和 Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

完成這個步驟的確認之後，您可以切換回 `asm` 模式。

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>步驟 4：選項 1 - 移轉雲端服務中的虛擬機器
使用下列命令來取得雲端服務清單，然後選擇您想要移轉的雲端服務。 請注意，如果雲端服務中的 VM 是在虛擬網路中，或是具有 Web/背景工作角色，您將會收到錯誤訊息。

    azure service list

執行下列命令，以從詳細資訊輸出中獲得雲端服務的部署名稱。 在大部分情況下，部署名稱和雲端服務名稱相同。

    azure service show <serviceName> -vv

準備好雲端服務中的虛擬機器以進行移轉。 有兩個選項可供您選擇。

如果您想要將 VM 移轉至平台建立的虛擬網路，請使用下列命令。

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

如果您想要移轉至 Resource Manager 部署模型中的現有虛擬網路，請使用下列命令。

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

準備作業成功之後，您可以瀏覽詳細資訊輸出，以取得 VM 的移轉狀態並確保 VM 處於 `Prepared` 狀態。

    azure vm show <vmName> -vv

使用 CLI 或 Azure 入口網站來檢查已備妥之資源的組態。 如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

    azure service deployment abort-migration <serviceName> <deploymentName>

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>步驟 4：選項 2 - 移轉虛擬網路中的虛擬機器
選取您想要移轉的虛擬網路。 請注意，如果虛擬網路包含 Web/背景工作角色，或有具備不支援之組態的 VM，您將會收到驗證錯誤訊息。

使用下列命令來取得訂用帳戶中的所有虛擬網路。

    azure network vnet list

輸出會看起來類似這樣：

![將整個虛擬網路名稱醒目提示的命令列螢幕擷取畫面。](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

在上述範例中，**virtualNetworkName** 是 **"Group classicubuntu16 classicubuntu16"** 這整個名稱。

使用下列命令來準備您所選擇的虛擬網路以進行移轉。

    azure network vnet prepare-migration <virtualNetworkName>

使用 CLI 或 Azure 入口網站來檢查已備妥之虛擬機器的組態。 如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

    azure network vnet abort-migration <virtualNetworkName>

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>步驟 5：移轉儲存體帳戶
完成虛擬機器移轉之後，我們建議您將移轉儲存體帳戶。

使用下列命令來準備儲存體帳戶以進行移轉

    azure storage account prepare-migration <storageAccountName>

使用 CLI 或 Azure 入口網站來檢查已備妥之儲存體帳戶的設定。 如果您尚未準備好進行移轉，而想要回到舊狀態，請使用下列命令。

    azure storage account abort-migration <storageAccountName>

如果備妥的組態看起來沒問題，您就可以繼續進行並使用下列命令來認可資源。

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>後續步驟
* [平台支援的 IaaS 資源移轉 (從傳統移轉至 Resource Manager)](virtual-machines-windows-migration-classic-resource-manager.md)
* [平台支援的從傳統移轉至 Resource Manager 的技術深入探討](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)




<!--HONumber=Jan17_HO4-->


