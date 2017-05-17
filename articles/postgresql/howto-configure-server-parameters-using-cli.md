---
title: "在適用於 PostgreSQL 的 Azure 資料庫中設定服務參數 | Microsoft Docs"
description: "描述如何在適用於 PostgreSQL 的 Azure 資料庫中設定服務參數。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>使用 Azure CLI 自訂伺服器設定參數
您可以使用命令列介面 (Azure CLI) 列出、顯示和更新 Azure PostgreSQL 伺服器的設定參數。 不過，只有一部分引擎設定會在伺服器層級公開而可供修改。 

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- 伺服器和資料庫 [建立適用於 PostgreSQL 的 Azure 資料庫](quickstart-create-server-database-azure-cli.md)
- 安裝 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 命令列公用程式

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>列出 Azure PostgreSQL 伺服器的伺服器設定參數
若要取得 Azure PostgreSQL 伺服器中所有可修改的參數及其值的清單，請執行如下所示的 **az postgres server configuration** 命令︰
> az postgres server configuration list --resource-group <resource group name> --server <server name>

例如，您可以針對資源群組 **myresourcegroup** 下的 Azure PostgreSQL 伺服器 **mypgserver.postgres.database.azure.com**，列出伺服器設定參數。
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>顯示伺服器設定參數的詳細資料
若要顯示有關 Azure PostgreSQL 伺服器之特定設定參數的詳細資訊，請執行 **az postgres server configuration** 命令。
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
例如，您可以針對資源群組 **myresourcegroup** 下的 Azure PostgreSQL 伺服器 **mypgserver.postgres.database.azure.com**，顯示 **log\_min\_messages** 伺服器設定參數的詳細資料 **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>修改伺服器設定參數值
您可以修改 Azure PostgreSQL 伺服器之特定設定參數的值，這樣會更新 PostgreSQL 伺服器引擎的基礎設定值。 若要更新設定值，執行下列 **az postgres server configuration** 命令。 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
例如，您可以針對資源群組 **myresourcegroup** 下的 Azure PostgreSQL 伺服器 **mypgserver.postgres.database.azure.com**，更新 **log\_min\_messages** 伺服器設定參數 **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
如果您想要將設定參數的值重設，只需要選擇保留選擇性 --value 參數即可，服務會套用預設值。 在上述範例中，看起來應該像這樣：
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
這將會將 log\_min\_messages 設定重設為預設值 WARNING。 如需伺服器設定和允許值的進一步詳細資訊，請參閱有關[伺服器設定](https://www.postgresql.org/docs/9.6/static/runtime-config.html)的 PostgreSQL 文件。

## <a name="next-steps"></a>後續步驟
- 若要設定及存取伺服器記錄，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄](concepts-server-logs.md)
