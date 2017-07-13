---
title: "Azure CLI 指令碼 - Azure Cosmos DB 的多區域複寫 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - Azure Cosmos DB 的多區域複寫"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: e797fef1ca889460dccd609ae0c769dc2933c2ac
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---

<a id="replicate-an-azure-cosmos-db-database-account-in-multiple-regions-and-configure-failover-priorities-using-the-azure-cli" class="xliff"></a>

# 使用 Azure CLI 複寫多個區域中的 Azure Cosmos DB 資料庫帳戶和設定容錯移轉優先順序

此範例會使用 Azure CLI 複寫多個區域中的任何一種 Azure Cosmos DB 資料庫帳戶，並設定容錯移轉優先順序。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

<a id="sample-script" class="xliff"></a>

## 範例指令碼

[!code-azurecli-interactive[主要](../../../cli_scripts/cosmosdb/scale-cosmosdb-replicate-multiple-regions/scale-cosmosdb-replicate-multiple-regions.sh?highlight=21-31 "將 Azure Cosmos DB 擴展至多個區域")]

<a id="clean-up-deployment" class="xliff"></a>

## 清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

<a id="script-explanation" class="xliff"></a>

## 指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | 更新 Azure Cosmos DB 帳戶。 |
| [az group delete](/cli/azure/resource#delete) | 刪除資源群組，包括所有的巢狀資源。 |

<a id="next-steps" class="xliff"></a>

## 後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Cosmos DB CLI 文件](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 指令碼範例。

