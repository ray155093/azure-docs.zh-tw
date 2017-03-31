---
title: "Azure CLI：建立 SQL Database | Microsoft Docs"
description: "了解如何使用 Azure CLI 建立 SQL Database 邏輯伺服器、伺服器層級防火牆規則和資料庫。"
keywords: "sql database 教學課程, 建立 sql database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3d642447526c5562ae3bfad5e4a4592e33e766aa
ms.lasthandoff: 03/25/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>使用 Azure CLI 建立單一 Azure SQL Database

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本指南詳述如何使用 Azure CLI 在 [Azure SQL Database 邏輯伺服器](sql-database-features.md)的 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)中部署 Azure SQL Database。

開始之前，請確定已安裝 Azure CLI。 如需詳細資訊，請參閱 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="log-in-to-azure"></a>登入 Azure

使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](../azure-resource-manager/resource-group-overview.md) 命令來建立 [Azure 資源群組](/cli/azure/group#create)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組。

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>建立邏輯伺服器

使用 [az sql server create](/cli/azure/sql/server#create) 命令建立 [Azure SQL Database 邏輯伺服器](sql-database-features.md)。 邏輯伺服器包含一組當作群組管理的資料庫。 下列範例會使用名為 `ServerAdmin` 的系統管理員登入和密碼 `ChangeYourAdminPassword1` 在資源群組中建立隨機命名的伺服器。 視需要取代這些預先定義的值。

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>設定伺服器防火牆規則

使用 [az sql server firewall create](/cli/azure/sql/server/firewall#create) 命令建立 [Azure SQL Database 伺服器層級防火牆規則](sql-database-firewall-configure.md)。 伺服器層級防火牆規則可讓外部應用程式 (例如 SQL Server Management Studio 或 SQLCMD 公用程式) 穿過 SQL Database 服務防火牆連線到 SQL Database。 下列範例會建立適用於預先定義之位址範圍的防火牆規則，在此範例中，這是整個可能的 IP 位址範圍。 以外部 IP 位址或 IP 位址範圍的值取代這些預先定義的值。 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server-with-sample-data"></a>在伺服器中建立資料庫與範例資料

使用 [az sql db create](/cli/azure/sql/db#create) 命令在伺服器中建立具有 [S0 效能等級](sql-database-service-tiers.md)的資料庫。 下列範例會建立名為 `mySampleDatabase` 的資料庫，並將 AdventureWorksLT 範例資料載入此資料庫。 視需要取代這些預先定義的值 (本集合中的其他快速入門會建置在本快速入門中的值)。

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門會建置在本快速入門。 如果您打算繼續進行後續的快速入門或教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列命令來刪除本快速入門建立的所有資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

- 若要使用 SQL Server Management Studio 來連線和查詢，請參閱[使用 SSMS 連線及查詢](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio 來連線，請參閱[使用 Visual Studio 連線及查詢](sql-database-connect-query.md)。
- 如需 SQL Database 的技術概觀，請參閱[關於 SQL Database 服務](sql-database-technical-overview.md)。

