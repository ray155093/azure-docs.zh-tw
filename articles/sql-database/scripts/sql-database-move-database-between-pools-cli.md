---
title: "Azure CLI 指令碼 - 移動 SQL Database 和彈性集區 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 使用 Azure CLI 在彈性集區之間移動 SQL Database"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 4e5775784458054413ff36f8cf72014739134ae5
ms.lasthandoff: 03/10/2017

---

# <a name="create-elastic-pools-and-move-databases-between-pools-and-out-of-a-pool-using-the-azure-cli"></a>使用 Azure CLI 建立彈性集區並在集區之間移動資料庫以及將其移出集區

此範例 CLI 指令碼會建立兩個彈性集區，並將資料庫從其中一個彈性集區移到另一個彈性集區，然後將資料庫從彈性集區移出到單一資料庫效能等級。 

在執行這個指令碼之前，請確定您已使用 `az login` 命令建立與 Azure 的連線。 

這個範例適用於 Bash 殼層。 如需在 Windows 上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "在集區之間移動資料庫")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | 建立主機資料庫或彈性集區的邏輯伺服器。 |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | 在邏輯伺服器內建立彈性集區。 |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 在邏輯伺服器中將資料庫建立為單一或集區的資料庫。 |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | 更新資料庫屬性，或將資料庫移入、移出彈性集區或在彈性集區之間移動資料庫。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../sql-database-cli-samples.md)中找到。



