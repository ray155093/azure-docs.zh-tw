---
title: "在適用於 MySQL 的 Azure 資料庫中使用傾印和還原來移轉 MySQL 資料庫 | Microsoft Docs"
description: "介紹如何移轉適用於 MySQL 的 Azure 資料庫。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c09a6fa947d235189ab0137b074b6d7d9c925827
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>使用傾印和還原來將 MySQL 資料庫移轉至適用於 MySQL 的 Azure 資料庫
本文將說明兩個常見方法，讓您可在適用於 MySQL 的 Azure 資料庫中用來備份和還原資料庫
- 從命令列備份和還原 (使用 mysqldump) 
- 使用 PHPMyAdmin 備份和還原 

## <a name="before-you-begin"></a>開始之前
若要逐步執行本作法指南，您需要具備：
- [建立適用於 MySQL 的 Azure 資料庫伺服器 - Azure 入口網站](quickstart-create-mysql-server-database-using-azure-portal.md)
- 已安裝於電腦上的 [mysqldump (英文)](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) 命令列公用程式
- MySQL Workbench [MySQL Workbench 下載 (英文)](https://dev.mysql.com/downloads/workbench/)、Toad、Navicat 或任何協力廠商的 MySQL 工具

## <a name="use-common-tools"></a>使用一般工具
使用一般工具 (例如 MySQL Workbench、mysqldump、Toad 或 Navicat) 從遠端連接，然後將資料還原至適用於 MySQL 的 Azure 資料庫。 在具有網際網路連接的用戶端電腦上使用這類工具，來連接到適用於 MySQL 的 Azure 資料庫。 如需使用 SSL 加密連接的最佳安全性作法，請參閱[在適用於 MySQL 的 Azure 資料庫中設定 SSL 連線能力](concepts-ssl-connection-security.md)。 在移轉到適用於 MySQL 的 Azure 資料庫時，您不需要將傾印檔案移至任何特定的雲端位置。 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>使用 mysqldump 從命令列建立備份檔案
若要備份內部部署或 VM 中的現有 MySQL 資料庫，請執行下列命令： 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

提供的參數如下：
- [uname] 您的資料庫使用者名稱 
- [pass] 您的資料庫密碼 (請注意 -p 與密碼之間沒有空格) 
- [dbname] 您的資料庫名稱 
- [backupfile.sql] 資料庫備份的檔案名稱 
- [--opt] mysqldump 選項 

例如，若要將使用者名稱為 'testuser' 且無密碼之名為 'testdb' 的資料庫備份到 testdb_backup.sql 檔案，請使用下列命令。 此命令會將 'testdb' 資料庫備份到名為 testdb_backup.sql 的檔案，其中將包含重新建立資料庫所需的所有 SQL 陳述式。 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
若要在資料庫中選取要備份的特定資料表，請列出以空格分隔的資料表名稱。 例如，如果只要從 'testdb' 備份 table1 和 table2 資料表，請遵循下列範例： 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

若要一次備份多個資料庫，請使用 --database 參數，並列出以空格分隔的資料庫名稱。 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
若要一次備份伺服器中的所有資料庫，您應該使用 --all-databases 選項。
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="upload-files"></a>上傳檔案
利用 WinSCP，您可以基於匯出目的，在本機透過 SFTP 通訊協定或 FTPS 通訊協定，輕鬆地上傳並管理現有 MySQL 環境 (Azure 或非 Azure) 檔案的匯入或傾印。

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>在目標 Azure MySQL 服務上建立資料庫
您必須在目標適用於 MySQL 的 Azure 資料庫伺服器上建立空的資料庫，您想要在該伺服器上使用 MySQL Workbench、Toad、Navicat 或任何適用於 MySQL 的協力廠商工具來移轉資料。 資料庫名稱可以與包含傾印資料的資料庫名稱相同，或者您可以建立名稱不同的資料庫。

![適用於 MySQL 的 Azure 資料庫的連接字串](./media/concepts-migrate-import-export/p5.png)

![MySQL Workbench 連接字串](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>使用命令列或 MySQL Workbench 來還原 MySQL 資料庫
建立目標資料庫後，您可以使用 mysql 命令或 MySQL Workbench，從傾印檔案將資料還原至新建立的特定資料庫。
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
在此範例中，我們會將資料還原至目標伺服器上新建立的資料庫 testdb3。
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>使用 PHPMyAdmin 匯出
若要匯出，您可以使用一般工具 phpMyAdmin，而您可能已在環境中本機安裝此工具。 使用 PHPMyAdmin 匯出 MySQL 資料庫：
- 開啟 phpMyAdmin。
- 在畫面左邊的清單中，按一下資料庫名稱來選取您的資料庫。 
- 按一下 [匯出] 連結。 這應該會顯示一個新畫面，指出「檢視資料庫的傾印」。 
- 在 [匯出] 區域中，按一下 [全選] 連結來選擇資料庫中的所有資料表。 
- 在 [SQL 選項] 區域中，按一下適當的選項。 
- 依序按一下 [另存新檔] 和對應的壓縮選項，然後按一下 [執行] 按鈕。 接著應該會出現一個對話方塊，提示您在本機儲存檔案。

## <a name="import-using-phpmyadmin"></a>使用 PHPMyAdmin 匯入
匯入資料庫的程序與匯出類似。 請執行下列動作：
- 開啟 phpMyAdmin。 
- 建立已適當命名的資料庫，然後在畫面左邊的清單中按一下資料庫名稱來選取它。 如果您想要對現有的資料庫重寫匯入，則可按一下資料庫名稱、選取資料表名稱旁邊所有的核取方塊，然後選取 [卸除] 以刪除資料庫中所有的現有資料表。 
- 按一下 SQL 連結。 這應該會顯示一個新畫面，您可以在此處輸入 SQL 命令，或上傳您的 SQL 檔案。 
- 您可以使用瀏覽按鈕來尋找資料庫檔案。 
- 按一下 [執行] 按鈕。 這將會匯出備份、執行 SQL 命令，並重新建立您的資料庫。

## <a name="next-steps"></a>後續步驟

[使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md) 
[使用 Azure CLI 建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-cli.md)

