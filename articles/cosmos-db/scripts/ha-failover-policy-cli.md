---
title: "Azure CLI 指令碼 - 建立高可用性的容錯移轉原則 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立高可用性的容錯移轉原則"
services: cosmosdb
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 285e50ed5e0596bb18e2fb9124992af34da71f1e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-failover-policy-for-high-availability-using-the-azure-cli"></a>使用 Azure CLI 建立高可用性的容錯移轉原則

此範例 CLI 指令碼會建立 Azure Cosmos DB 帳戶，然後將它設定為高可用性。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/cosmosdb/high-availability-cosmosdb-configure-failover/high-availability-cosmosdb-configure-failover.sh?highlight=23-27 "建立 Azure Cosmos DB 容錯移轉原則")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az cosmosdb create](/cli/azure/sql/server#create) | 建立 Azure Cosmos DB 帳戶。 |
| [az cosmosdb update](/cli/azure/cosmosdb#update) | 更新 Azure Cosmos DB 帳戶。 |
| [az group delete](/cli/azure/resource#delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Cosmos DB CLI 文件](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 指令碼範例。

