---
title: "快速入門：建立 Azure Database for MySQL 伺服器 - Azure 入口網站 | Microsoft Docs"
description: "本文逐步引導您使用 Azure 入口網站在五分鐘內快速建立範例 Azure Database for MySQL 伺服器。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 85bb0396809297f0efb7323bef081055f17ede62
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>使用 Azure 入口網站建立 Azure Database for MySQL 伺服器

本文逐步引導您使用 Azure 入口網站在五分鐘內建立範例 Azure Database for MySQL 伺服器。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-azure"></a>登入 Azure
開啟 Web 瀏覽器並瀏覽至 [Microsoft Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入入口網站。 預設檢視是您的服務儀表板。

## <a name="create-azure-database-for-mysql-server"></a>建立 Azure Database for MySQL 伺服器

1. 瀏覽至 [資料庫] > [MySQL]。 如果您在 [資料庫] 類別下找不到 Azure Database for MySQL 伺服器，請按一下 [查看全部] 以顯示所有可用的資料庫服務。 您也可以在搜尋方塊中輸入 **MySQL** 以快速找到此服務。
![Azure 入口網站 - 新增 - 資料庫 - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

2. 對 [MySQL] 圖示，然後按一下 [建立]。
在範例中，以下列資訊填妥 Azure Database for MySQL 頁面︰

| **表單欄位** | **欄位描述** |
|----------------|-----------------------|
| *伺服器名稱* | mysqlserver4demo (伺服器名稱是全域唯一的) |
| *訂用帳戶* | MySQLaaS (從下拉式清單中選取) |
| *資源群組* | myresource (建立資源群組，或使用現有的資源群組) |
| *伺服器管理員登入* | myadmin (設定管理帳戶名稱) |
| *密碼* | 設定管理帳戶密碼 |
| *確認密碼* | 確認管理帳戶密碼 |
| *位置* | 北歐 (選取 [北歐] 或 [美國西部]) |
| *版本* | 5.6 (選擇 MySQL 伺服器版本) |
| *設定效能* | 基本 (選擇 [效能層級]、[計算單位]、[儲存體]，然後按一下 [確定]) |

![Azure 入口網站 - 提供需要表單輸入以建立 MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

幾分鐘之後，將會佈建並執行 Azure Database for MySQL 伺服器。 您可以按一下工具列上的 [通知] 按鈕 (鈴鐺圖示) 來監視部署程序。

> [!TIP]
> 我們建議您將 Azure 服務放在相同區域中，並選取最接近您的位置。 此外，您可以勾選 [釘選到儀表板] 選項以輕鬆追蹤部署。

## <a name="configure-the-firewall"></a>設定防火牆
第一次從用戶端連線到 Azure Database for MySQL 之前，您必須設定防火牆，並將用戶端的公用網路的 IP 位址 (或 IP 位址範圍) 新增至允許清單。

1. 按一下您剛建立的伺服器，然後按一下 [設定]。
  ![Azure 入口網站 - MySQL - 設定按鈕](./media/quickstart-create-mysql-server-database-using-azure-portal/4_server-settings.png)

2. 按一下 [一般] 區段下的 [防火牆設定]。 您可以按一下 [新增我的 IP] 來新增本機電腦的 IP 位址，或設定 IP 位址範圍。 請記得在建立規則後按一下 [儲存]。
  ![Azure 入口網站 - 新增防火牆規則並儲存](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>取得連線資訊
在 Azure 入口網站中取得 Azure MySQL 伺服器的完整伺服器名稱。 您可使用 **mysql.exe** 命令列工具，使用此完整網域名稱連線到您的伺服器。

1.    在 [Azure 入口網站](https://portal.azure.com/)中，按一下左側功能表中的 [所有資源]，然後按一下您的 Azure Database for MySQL 伺服器。

2.    按一下 [內容] 。 記下 [伺服器名稱] 和 [伺服器管理員登入]。
在此範例中，伺服器名稱為 mysql4doc.database.windows.net，而伺服器管理員登入為 mysqladmin@mysql4doc。

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>使用 mysqlexe 命令列工具連線到伺服器
您可以在一部 MySQL 伺服器內建立多個資料庫。 可以建立的資料庫數目沒有限制，但多個資料庫會共用相同的伺服器資源。  若要使用 **mysql.exe** 命令列工具連線到伺服器，請在入口網站中開啟 **Azure Cloud Shell**，然後輸入下列資訊︰

1. 使用 **mysql** 命令列工具連線到伺服器︰
```dos
 mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

2. 檢視伺服器狀態：
```dos
 mysql> status
```
  ![命令提示字元 - mysql 命令列範例](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

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

![設定新連線](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

3.    按一下 [測試連線] 以測試所有參數是否都已設定正確。

4.    您現在可以按一下剛建立的連線，以成功連線到伺服器。

> 預設會在您的伺服器上強制執行 SSL，該伺服器需要額外的設定才能連線成功。 請參閱[在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫](./howto-configure-ssl.md)。  如果您想要在此快速入門中停用 SSL，您可以移至入口網站中的「連線安全性」來停用強制執行 SSL。

## <a name="clean-up-resources"></a>清除資源

如果您不需要這些資源來進行其他快速入門/教學課程，您可以執行下列作業加以刪除︰

1. 從 Azure 入口網站的左側功能表中，依序按一下 [資源群組] 和 [myresource]。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入 **myresource**，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設計您的第一個 Azure Database for MySQL 資料庫](./tutorial-design-database-using-portal.md)


