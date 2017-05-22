---
title: "使用 Azure CLI 來設計您第一個適用於 PostgreSQL 的 Azure 資料庫 | Microsoft Docs"
description: "本教學課程說明如何使用 Azure CLI 來設計您的第一個「適用於 PostgreSQL 的 Azure 資料庫」。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fbac141243f0f36aab1a2ef9f28b4e107fd2d390
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>使用 Azure CLI 來設計您第一個適用於 PostgreSQL 的 Azure 資料庫 
在本教學課程中，您將使用 Azure CLI (命令列介面) 及其他公用程式來學習如何：
> [!div class="checklist"]
> * 建立適用於 PostgreSQL 的 Azure 資料庫
> * 設定伺服器防火牆
> * 使用 [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) 公用程式來建立資料庫
> * 載入範例資料
> * 查詢資料
> * 更新資料
> * 還原資料

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>登入 Azure

使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。
```azurecli
az login
```

如果您有多個訂用帳戶，請選擇資源所在或作為計費對象的適當訂用帳戶。 使用 [az account set](/cli/azure/account#set) 命令來選取您帳戶底下的特定訂用帳戶 ID。
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](../azure-resource-manager/resource-group-overview.md) 命令建立 [Azure 資源群組](/cli/azure/group#create)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 `westus` 位置建立名為 `myresourcegroup` 的資源群組。
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

使用 **az postgres server create** 命令來建立[適用於 PostgreSQL 的 Azure 資料庫伺服器](overview.md)。 一個伺服器會包含一組以群組方式管理的資料庫。 

下列範例會使用伺服器管理員登入 `mylogin` 在資源群組 `myresourcegroup` 中建立名為 `mypgserver-20170401` 的伺服器。 伺服器的名稱會與 DNS 名稱對應，因此在 Azure 中必須是全域唯一的。 將 `<server_admin_password>` 替換成您自己的值。
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> 需要伺服器系統管理員登入以及您在此處指定的密碼，稍後才能在本快速入門中登入伺服器及其資料庫。 請記住或記錄此資訊，以供稍後使用。

根據預設，**postgres** 資料庫會建立在您的伺服器底下。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) 資料庫是要供使用者、公用程式及協力廠商應用程式使用的預設資料庫。 


## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則

使用 **az postgres server firewall-rule create** 命令來建立 Azure PostgreSQL 伺服器層級防火牆規則。 伺服器層級防火牆規則可允許外部應用程式 (例如 [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) 或 [PgAdmin](https://www.pgadmin.org/)) 穿過 Azure PostgreSQL 服務防火牆連線到您的伺服器。 

您可以設定一個防火牆規則，來涵蓋能夠從您網路連線的 IP 範圍。 下列範例使用 **az postgres server firewall-rule create** 來建立某個 IP 位址範圍的防火牆規則 `AllowAllIps`。 若要開啟所有 IP 位址，請使用 0.0.0.0 作為起始 IP 位址，並使用 255.255.255.255 作為結束位址。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL 伺服器會透過連接埠 5432 進行通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 5432 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 5432，否則您將無法連線到 Azure SQL Database 伺服器。
>

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，您必須提供主機資訊和存取認證。
```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

結果會採用 JSON 格式。 請記下 **administratorLogin** 和 **fullyQualifiedDomainName**。
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>使用 psql 來連線到適用於 PostgreSQL 資料庫的 Azure 資料庫

如果您的用戶端電腦已安裝 PostgreSQL，您就可以使用 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 的本機執行個體來連線到 Azure PostgreSQL 伺服器。 現在我們將使用 psql 命令列公用程式來連線到 Azure PostgreSQL 伺服器。

1. 執行下列 psql 命令以連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  例如，下列命令會使用存取認證，連線到 PostgreSQL 伺服器 **mypgserver-20170401.postgres.database.azure.com** 上名為 **postgres** 的預設資料庫。 系統提示輸入密碼時，請輸入您選擇的 `<server_admin_password>`。
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  連線到伺服器後，在提示字元建立空白資料庫。
```bash
CREATE DATABASE mypgsqldb;
```

3.  在提示字元，執行下列命令以將連線切換到新建立的資料庫 **mypgsqldb**：
```bash
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>在資料庫中建立資料表
既然您已知道如何連線到「適用於 PostgreSQL 的 Azure 資料庫」，我們可以了解一下如何完成一些基本工作。

首先，我們可以建立資料表並在其中載入一些資料。 我們將建立一個追蹤清查資訊的資料表。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

您現在可以輸入下列命令來查看資料表清單中新建立的資料表：
```bash
\dt
```

## <a name="load-data-into-the-tables"></a>將資料載入到資料表
既然我們已有資料表，我們可以在其中插入一些資料。 在開啟的命令提示字元視窗，執行下列查詢以插入幾列資料
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

您現在已將兩列範例資料插入到先前建立的資料表。

## <a name="query-and-update-the-data-in-the-tables"></a>查詢並更新資料表中的資料
執行下列查詢，以從資料庫資料表中擷取資訊。 
```sql
SELECT * FROM inventory;
```

您也可以更新資料表中的資料
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

當您擷取資料時，資料列會相應地更新。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>將資料庫還原至先前的時間點
假設您不小心刪除了資料表。 這是您無法輕易復原的情況。 「適用於 PostgreSQL 的 Azure 資料庫」可讓您返回到任何時間點 (最長可達過去 7 天 (基本) 和 35 天 (標準))，並將此時間點還原到新資料庫。 您可以使用這個新的伺服器來復原已刪除的資料。 下列步驟會將範例伺服器還原到新增資料表之前的時間點。

若要進行還原，您需要下列資訊︰
- **還原點：**選取在變更伺服器之前的時間點。 必須大於或等於來源資料庫的最舊備份值。
- **目標伺服器︰**提供要作為還原目的地的新伺服器名稱
- **來源伺服器︰**提供要作為還原來源的伺服器名稱
- **位置︰**您無法選取區域，預設是與來源伺服器相同的區域
- **定價層︰**還原伺服器時，您無法變更此值。 它與來源伺服器相同。 

```azurecli
az postgres server restore --resource-group myResourceGroup --name mypgserver-20170401-restored --restore-point-in-time "2017-04-13 03:10" --source-server-name mypgserver-20170401
```

將伺服器[還原到刪除資料表之前的時間點](./howto-restore-server-portal.md)。 如果將伺服器還原到不同的時間點，將會從您指定的時間點 (前提是此時間點在您[服務層](./concepts-service-tiers.md)的保留期限內) 開始，建立重複的新伺服器作為原始伺服器。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何使用 Azure CLI (命令列介面) 及其他公用程式來：
> [!div class="checklist"]
> * 建立適用於 PostgreSQL 的 Azure 資料庫
> * 設定伺服器防火牆
> * 使用 [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) 公用程式來建立資料庫
> * 載入範例資料
> * 查詢資料
> * 更新資料
> * 還原資料

接著，了解如何使用 Azure 入口網站來執行類似的工作，請檢閱此教學課程：[使用 Azure 入口網站來設計您第一個適用於 PostgreSQL 的 Azure 資料庫](tutorial-design-database-using-azure-portal.md)

