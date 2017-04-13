---
title: "Azure CLI 指令碼範例 - 使用 Batch 執行工作 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 使用 Batch 執行工作"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>在 Azure Batch 上使用 Azure CLI 執行工作

此指令碼會建立 Batch 工作，並將一系列作業加入至工作。 它也示範如何監視工作和其作業。
執行這個指令碼的前提是您已設定 Batch 帳戶，並已設定集區和應用程式。 如需詳細資訊，請參閱涵蓋各主題的[範例指令碼](../batch-cli-samples.md)。

您可以視需要使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的指示來安裝 Azure CLI，然後執行 `az login` 以登入 Azure。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/batch/run-job/run-job.sh "執行工作")]

## <a name="clean-up-job"></a>清除工作

執行上述範例指令碼之後，請執行下列命令以移除工作和其所有作業。 請注意，集區必須另外刪除；請參閱[管理集區的教學課程](./batch-cli-sample-manage-pool.md)。

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立 Batch 工作和作業。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | 對 Batch 帳戶進行驗證。  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | 建立 Batch 工作。  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | 更新 Batch 工作的屬性。  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | 擷取指定的 Batch 工作的詳細資料。  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | 將作業加入至指定的 Batch 工作。  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | 從指定的 Batch 工作擷取作業的詳細資料。  |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Batch CLI 文件](../batch-cli-samples.md)中找到其他的 Batch CLI 指令碼範例。

