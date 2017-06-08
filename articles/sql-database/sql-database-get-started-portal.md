---
title: "Azure 入口網站：建立 SQL Database | Microsoft Docs"
description: "了解如何在 Azure 入口網站中，建立 SQL Database 邏輯伺服器、伺服器層級防火牆規則和資料庫。 您也可以了解如何使用 Azure 入口網站查詢 Azure SQL Database。"
keywords: "sql database 教學課程, 建立 sql database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 05/30/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ab664eb8803037b0bf09399bf9d28059fec2e204
ms.contentlocale: zh-tw
ms.lasthandoff: 06/01/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure SQL Database

本快速入門教學課程逐步解說如何在 Azure 中建立 SQL Database。 Azure SQL Database 是可讓您在雲端中執行及調整高可用性 SQL Server 資料庫的「資料庫即服務」供應項目。 本快速入門會說明如何使用 Azure 入口網站建立 SQL Database 以開始使用。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-sql-database"></a>建立 SQL 資料庫

Azure SQL Database 會使用一組定義的[計算和儲存體資源](sql-database-service-tiers.md)建立。 此資料庫建立於 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL Database 邏輯伺服器](sql-database-features.md)內。 

請遵循下列步驟來建立包含 Adventure Works LT 範例資料的 SQL Database。 

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [SQL Database]。

   ![建立資料庫-1](./media/sql-database-get-started-portal/create-database-1.png)

3. 使用下列資訊填寫 SQL Database 表單，如上圖所示︰   

   | 設定       | 建議的值 | 說明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **資料庫名稱** | mySampleDatabase | 如需有效的資料庫名稱，請參閱[資料庫識別碼](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers)。 | 
   | **訂用帳戶** | 您的訂用帳戶  | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組**  | myResourceGroup | 如需有效的資源群組名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 |
   | **來源** | 範例 (AdventureWorksLT) | 將 AdventureWorksLT 結構描述和資料載入至新的資料庫 |

   > [!IMPORTANT]
   > 您必須在此表單上選取範例資料庫，因為它會用於本快速入門的其餘部分。
   > 

4. 使用下列資訊填寫 SQL Server (邏輯伺服器) 表單，如上圖所示︰   

   | 設定       | 建議的值 | 說明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **伺服器名稱** | 任何全域唯一名稱 | 如需有效的伺服器名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 | 
   | **伺服器管理員登入** | 任何有效名稱 | 如需有效的登入名稱，請參閱[資料庫識別碼](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers)。 |
   | **密碼** | 任何有效密碼 | 您的密碼至少要有 8 個字元，而且必須包含下列幾種字元的其中三種︰大寫字元、小寫字元、數字和非英數字元。 |
   | **訂用帳戶** | 您的訂用帳戶 | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組** | myResourceGroup | 如需有效的資源群組名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 |
   | **位置** | 任何有效位置 | 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |

   > [!IMPORTANT]
   > 需要伺服器系統管理員登入以及您在此處指定的密碼，稍後才能在本快速入門中登入伺服器及其資料庫。 請記住或記錄此資訊，以供稍後使用。 
   >  

   ![建立資料庫伺服器](./media/sql-database-get-started-portal/create-database-server.png)

5. 完成表單後，按一下 [選取]。

6. 按一下 [定價層] 指定新資料庫的服務層和效能等級。 使用滑桿來選取 [20 個 DTU] 和 [250] GB 的儲存體。 如需 DTU 的詳細資訊，請參閱[什麼是 DTU？](sql-database-what-is-a-dtu.md)。

   ![建立資料庫-s1](./media/sql-database-get-started-portal/create-database-s1.png)

7. 選取 DTU 數量之後，按一下 [套用]。  

8. 您現在已完成 SQL Database 表單，請按一下 [建立] 來佈建資料庫。 佈建需要幾分鐘的時間。 

9. 在工具列上，按一下 [通知] 以監視部署程序。

   ![通知](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>建立伺服器層級防火牆規則

SQL Database 服務會在伺服器層級建立防火牆，防止外部應用程式和工具連線到伺服器或伺服器上的任何資料庫，除非建立防火牆規則以針對特定的 IP 位址開啟防火牆。 請遵循下列步驟來為您用戶端的 IP 位址建立 [SQL Database 伺服器層級防火牆規則](sql-database-firewall-configure.md)，並讓外部連線僅能夠穿過您 IP 位址的 SQL Database 防火牆。 

> [!NOTE]
> SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若情況如此，除非 IT 部門開啟連接埠 1433，否則您無法連線至 Azure SQL Database 伺服器。
>

1. 部署完成之後，按一下左側功能表中的 [SQL Database]，然後按一下 [SQL Database] 頁面上的 [mySampleDatabase]。 資料庫的 [概觀] 頁面隨即開啟，其中會顯示完整伺服器名稱 (例如 **mynewserver20170313.database.windows.net**)，並提供進一步的組態選項。 將這個完整伺服器名稱複製起來，以供稍後使用。

   > [!IMPORTANT]
   > 在後續的快速入門中，您需要此完整伺服器名稱才能連線到伺服器及其資料庫。
   > 

   ![伺服器名稱](./media/sql-database-get-started-portal/server-name.png) 

2. 如先前映像所示，按一下工具列上的 [設定伺服器防火牆]。 SQL Database 伺服器的 [防火牆設定] 頁面隨即開啟。 

   ![伺服器防火牆規則](./media/sql-database-get-started-portal/server-firewall-rule.png) 

3. 按一下工具列上的 [新增用戶端 IP]，將目前的 IP 位址新增至新的防火牆規則。 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

4. 按一下 [儲存] 。 系統便會為目前的 IP 位址建立伺服器層級防火牆規則，以便在邏輯伺服器上開啟連接埠 1433。

   ![設定伺服器防火牆規則](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. 依序按一下 [確定]，然後關閉 [防火牆設定] 頁面。

您現在可以利用 SQL Server Management Studio 或選擇的其他工具，使用先前建立的伺服器管理帳戶從這個 IP 位址連線至 SQL Database 伺服器及其資料庫。

> [!IMPORTANT]
> 根據預設，已對所有 Azure 服務啟用透過 SQL Database 防火牆存取。 按一下此頁面上的 [關閉] 即可對所有 Azure 服務停用。
>

## <a name="query-the-sql-database"></a>查詢 SQL Database

您現在已在 Azure 中建立範例資料庫，讓我們使用 Azure 入口網站內建的查詢工具來確認您可以連線到資料庫並查詢資料。 

1. 在資料庫的 [SQL Database] 頁面上，按一下工具列上的 [工具]。 [工具] 頁面隨即開啟。

   ![[工具] 功能表](./media/sql-database-get-started-portal/tools-menu.png) 

2. 依序按一下 [查詢編輯器 (預覽)]、[預覽條款] 核取方塊和 [確定]。 [查詢編輯器] 頁面隨即開啟。

3. 按一下 [登入]，然後在出現提示時選取 [SQL Server 驗證]，接著提供您稍早建立的伺服器管理員登入和密碼。

   ![登入](./media/sql-database-get-started-portal/login.png) 

4. 按一下 [確定] 以登入。

5. 經過驗證後，在查詢編輯器窗格中輸入下列查詢。

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. 按一下 [執行]，然後在 [結果] 窗格中檢閱查詢結果。

   ![查詢編輯器結果](./media/sql-database-get-started-portal/query-editor-results.png)

7. 關閉 [查詢編輯器] 頁面和 [工具] 頁面。

## <a name="clean-up-resources"></a>清除資源

如果您不需要這些資源來進行其他快速入門/教學課程 (請參閱[後續步驟](#next-steps))，您可以執行下列作業加以刪除︰


1. 從 Azure 入口網站的左側功能表中，依序按一下 [資源群組] 和 [myResourceGroup]。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入 **myResourceGroup**，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

您現在具有資料庫，您可使用最愛的工具進行連線和查詢。 選擇下列工具來深入了解︰

- [SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [Visual Studio Code](sql-database-connect-query-vscode.md)
- [.NET](sql-database-connect-query-dotnet.md)
- [PHP](sql-database-connect-query-php.md)
- [Node.js](sql-database-connect-query-nodejs.md)
- [Java](sql-database-connect-query-java.md)
- [Python](sql-database-connect-query-python.md)
- [Ruby](sql-database-connect-query-ruby.md)

