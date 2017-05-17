---
title: "使用 Azure CLI 設定和存取 PostgreSQL 的伺服器記錄 | Microsoft Docs"
description: "描述如何在適用於 PostgreSQL 的 Azure 資料庫中設定和存取伺服器記錄。"
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
ms.openlocfilehash: dc047e6d5194035586931b3cacc4d5cd07f6cfc2
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>使用 Azure CLI 設定和存取伺服器記錄
您可以使用命令列介面 (Azure CLI) 列出和下載 Azure PostgreSQL 伺服器錯誤記錄。 但是，不支援存取交易記錄。 

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-azure-cli.md)
- 安裝 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 命令列公用程式

## <a name="configure-logging-for-azure-database-for-postgresql"></a>設定「適用於 PostgreSQL 的 Azure 資料庫」的記錄
您可以設定伺服器來存取查詢記錄和錯誤記錄。 錯誤記錄可能包含自動清空、連線和檢查點資訊。
1. 開啟記錄功能
2. 更新 log\_statement 和 log\_min\_duration\_statement 來啟用查詢記錄
3. 更新保留期限

如需詳細資訊，請參閱[自訂伺服器設定參數](howto-configure-server-parameters-using-cli.md)。

## <a name="list-logs-for-azure-postgresql-server"></a>列出 Azure PostgreSQL 伺服器的記錄
若要列出伺服器的記錄檔，請執行 **az postgres server-logs** 命令，如下列範例所示︰
```azurecli
az postgres server-logs list --resource-group <resource group name> --server <server name> [ --file-last-written --filename-contains --max-file-size ]
```
例如，您可以針對資源群組 **myresourcegroup** 下的 Azure PostgreSQL 伺服器 **mypgserver.postgres.database.azure.com**，列出 Azure PostgreSQL 伺服器的記錄檔，並輸出至名為 **log\_files\_list.txt** 的文字檔 **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup** --server **mypgserver** > log\_files\_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>從伺服器將記錄下載至本機
您也可以下載 Azure PostgreSQL 伺服器的個別記錄檔。 
```azurecli
az postgres server-logs download –name <log file name> --resource-group <resource group name> --server <server name>
```
此範例會針對資源群組 **myresourcegroup** 下的 Azure PostgreSQL 伺服器 **mypgserver.postgres.database.azure.com**，將特定的記錄檔下載至您的本機環境。
```azurecli
az postgres server-logs download --name 20170414-mypgserver-postgresql.log --resource-group **myresourcegroup** --server **mypgserver**
```
## <a name="next-steps"></a>後續步驟
- 若要深入了解伺服器記錄，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄](concepts-server-logs.md)
- 如需伺服器參數的詳細資訊，請參閱[使用 Azure CLI 自訂伺服器設定參數](howto-configure-server-parameters-using-cli.md)
