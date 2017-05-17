---
title: "建立連線至 Azure DocumentDB 的 Azure 函式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立連線至 Azure DocumentDB 的 Azure 函式"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 46e0fe827b7b34010d14a31ff377d4854ec62f6e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>建立連線至 Azure Cosmos DB 的 Azure 函式

此範例指令碼建立連線至 Azure Cosmos DB 資料庫的 Azure 函式應用程式。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

建立應用程式範例

[!code-azurecli[主要](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "建立連線至 Azure Cosmos DB 的 Azure 函式")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有相關資源。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | 登入 Azure。 |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 指定位置建立資源群組 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | 建立儲存體帳戶 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | 建立新的函式應用程式 |
| [az documentdb create](https://docs.microsoft.com/cli/azure/documentdb#create) | 建立 DocumentDB 資料庫 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | 清除 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Functions 文件](../functions-cli-samples.md)中找到其他 Azure Functions CLI 指令碼範例。





