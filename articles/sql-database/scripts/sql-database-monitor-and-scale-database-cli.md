---
title: "Azure CLI 指令碼 - 監視和調整單一 SQL Database | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 使用 Azure CLI 監視和調整單一 SQL Database"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/24/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: f29da889f90968a82dccaeb1fa7e3c20e6b44458
ms.contentlocale: zh-tw
ms.lasthandoff: 03/30/2017

---

# <a name="monitor-and-scale-a-single-sql-database-using-the-azure-cli"></a>使用 Azure CLI 監視和調整單一 SQL Database

此範例 CLI 指令碼會在查詢單一 Azure SQL Database 的大小資訊後，將其調整為不同的效能等級。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "監視和調整單一 SQL Database")]

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
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | 建立主機資料庫的邏輯伺服器。 |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#show-usage) | 顯示資料庫的大小使用量資訊。 |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | 更新資料庫屬性 (例如服務層或效能等級)，或將資料庫移入、移出彈性集區或在彈性集區之間移動資料庫。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

其他的 SQL Database CLI 指令碼範例可於 [Azure SQL Database 文件](../sql-database-cli-samples.md)中找到。

