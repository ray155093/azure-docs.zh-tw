---
title: "快速入門：建立 Azure Database for MySQL 伺服器 - Azuer CLI | Microsoft Docs"
description: "本快速入門說明如何使用 Azure CLI 在 Azure 資源群組中建立 Azure Database for MySQL 伺服器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 574299dd64120d75a1a36cb2ded0fdd269292570
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 建立 Azure Database for MySQL 伺服器
本快速入門說明如何使用 Azure CLI，在大約 5 分鐘內於 Azure 資源群組中建立 Azure Database for MySQL 伺服器。 Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。

若要完成本快速入門，請確定您已安裝最新的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-azure"></a>登入 Azure

使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurecli
az login
```
依照命令提示字元指示在瀏覽器中開啟 https://aka.ms/devicelog，然後輸入在**命令提示字元**中產生的程式碼。

## <a name="create-a-resource-group"></a>建立資源群組
使用 [az group create](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) 命令建立 [Azure 資源群組](https://docs.microsoft.com/cli/azure/group#create)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。

下列範例會在 `westus` 位置建立名為 `mycliresource` 的資源群組。

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器
使用 **az mysql server create** 命令建立 Azure Database for MySQL 伺服器。 一部伺服器可以管理多個資料庫。 一般而言，每個專案或每個使用者會使用個別的資料庫。

下列範例會建立名稱為 `mycliserver` 的 Azure Database for MySQL 伺服器，其位於資源群組 `mycliresource` 的 `westus` 中。 系統管理員以名稱 `myadmin` 和密碼 `Password01!` 登入此伺服器。 建立的伺服器為 [基本] 效能層級，並有 **50** 個計算單位在伺服器中的所有資料庫之間共用。 視應用程式需求而定，您可以相應增加或減少計算和儲存體。

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver--location westus --user myadmin --password Password01! --performance-tier Basic --compute-units 50
```

![使用 Azure CLI 建立 Azure Database for MySQL 伺服器](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>設定防火牆規則
使用 **az mysql server firewall-rule create** 命令建立 Azure Database for MySQL 伺服器層級的防火牆規則。 伺服器層級的防火牆規則允許外部應用程式 (例如 **mysql.exe** 命令列工具或 MySQL Workbench) 穿過 Azure MySQL 服務防火牆連線到您的伺服器。 

下列範例會建立適用於預先定義之位址範圍的防火牆規則，在此範例中，這是整個可能的 IP 位址範圍。

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>進行 SSL 設定
預設會強制執行您的伺服器與用戶端應用程式之間的 SSL 連線。  這可藉由加密透過網際網路的資料流，確保「移動中」資料的安全性。  為了讓本快速入門容易進行，我們將會停用您伺服器的 SSL 連線。  不建議對生產伺服器這麼做。  如需詳細資訊，請參閱[在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫](./howto-configure-ssl.md)。

下列範例會停用在 MySQL 伺服器上強制執行 SSL。
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，您必須提供主機資訊和存取認證。

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

結果為 JSON 格式。 請記下 **fullyQualifiedDomainName** 和 **administratorLogin**。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>使用 mysql.exe 命令列工具連線到伺服器
若要使用 **mysql.exe** 命令列工具連線到伺服器，務必在您的電腦上安裝 MySQL。  您可以從[這裡](https://dev.mysql.com/downloads/)下載 MySQL。

開啟命令提示字元並輸入下列資訊︰ 

1. 使用 **mysql** 命令列工具連線到伺服器︰
```dos
 mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

2. 檢視伺服器狀態：
```dos
 mysql> status
```
如果一切順利，命令列工具應輸出下列內容︰

```dos
C:\Users\v-chenyh>mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mycliserver.database.windows.net via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> 如需其他命令，請參閱 [MySQL 5.6 參考手冊 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)。

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>使用 MySQL Workbench GUI 工具連線到伺服器
1.    在您的用戶端電腦上啟動 MySQL Workbench 應用程式。 您可以從[這裡](https://dev.mysql.com/downloads/workbench/)下載並安裝 MySQL Workbench。

2.    在 [設定新連線] 對話方塊的 [參數] 索引標籤上輸入下列資訊︰

| **參數** | **說明** |
|----------------|-----------------|
|    連線名稱 | 指定此連線的名稱 (這可以是任何項目) |
| 連線方式 | 選擇標準 (TCP/IP) |
| 主機名稱 | mycliserver.database.windows.net (您先前記下的伺服器名稱) |
| *連接埠* | 3306 |
| *使用者名稱* | myadmin@mycliserver (您先前記下的伺服器管理員登入) |
| *密碼* | 您可以在保存庫中儲存系統管理帳戶密碼 |

![設定新連線](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

3.    按一下 [測試連線] 以測試所有參數是否都已設定正確。

4.    您現在可以按一下剛建立的連線，以成功連線到伺服器。

## <a name="clean-up-resources"></a>清除資源

如果您不需要這些資源來進行其他快速入門/教學課程，您可以執行下列作業加以刪除︰ 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 設計 MySQL 資料庫](./tutorial-design-database-using-cli.md).

