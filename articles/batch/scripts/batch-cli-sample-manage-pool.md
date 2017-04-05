---
title: "Azure CLI 指令碼範例 - 管理 Batch 中的集區 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 管理 Batch 中的集區"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7cec2a8c66868c601c38dc2dea61dda38d3cd3b2
ms.lasthandoff: 03/24/2017

---

# <a name="managing-azure-batch-pools-with-azure-cli"></a>使用 Azure CLI 管理 Azure Batch 集區

這些指令碼示範 Azure CLI 中一些可用的工具，用於建立和管理 Azure Batch 服務中的計算節點集區。

執行這些指令碼的前提是您已設定 Batch 帳戶，並已設定應用程式。 如需詳細資訊，請參閱涵蓋各主題的[範例指令碼](../batch-cli-samples.md)。

> [!NOTE]
> 此範例中的命令會建立 Azure 虛擬機器。 執行中的 VM 會累計費用至您的帳戶。 若要將這些費用降至最低，請在範例執行完成後隨即刪除 VM。 請參閱[清除集區](#clean-up-pools)。

您可以視需要使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的指示來安裝 Azure CLI，然後執行 `az login` 以登入 Azure。

有兩種方式可以設定 Batch 集區，即使用雲端服務組態 (僅限 Windows) 或虛擬機器組態 (Windows 和 Linux)。

## <a name="pool-with-cloud-service-configuration-sample-script"></a>使用雲端服務組態範例指令碼的集區

[!code-azurecli[主要](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "管理雲端服務集區")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>使用虛擬機器組態範例指令碼的集區

[!code-azurecli[主要](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "管理虛擬機器集區")]

## <a name="clean-up-pools"></a>清除集區

執行上述範例指令碼之後，請執行下列命令以刪除集區。
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立和管理 Batch 集區。
下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | 對 Batch 帳戶進行驗證。  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#list) | 列出 Batch 帳戶中可用的應用程式。  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#create) | 建立 VM 集區。  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#set) | 更新集區的屬性。  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#list) | 列出可用的節點代理程式 SKU 和映像資訊。  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#resize) | 調整指定集區的執行中 VM 數大小。  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#show) | 顯示集區的屬性。  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#delete) | 刪除指定的集區。  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#enable) | 在集區啟用自動調整規模並套用公式。  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#disable) | 在集區停用自動調整規模。  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#list) | 列出指定集區中的所有計算節點。  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#reboot) | 重新啟動指定的計算節點。  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#delete) | 從指定的集區中刪除列出的節點。  |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Batch CLI 文件](../batch-cli-samples.md)中找到其他的 Batch CLI 指令碼範例。


