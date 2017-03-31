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
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: be5839e04fae457b889db11dffe56f31afe723a5
ms.lasthandoff: 03/28/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure SQL Database

本快速入門教學課程逐步解說如何在 Azure 中建立 SQL Database。  Azure SQL Database 是可讓您在雲端中執行及調整高可用性 SQL Server 資料庫的「資料庫即服務」供應項目。  本快速入門會顯示如何使用 Azure 入口網站建立新的 SQL Database 以開始使用。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-sql-database"></a>建立 SQL 資料庫

Azure SQL Database 會使用一組定義的[計算和儲存體資源](sql-database-service-tiers.md)建立。 此資料庫建立於 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL Database 邏輯伺服器](sql-database-features.md)內。 

請遵循下列步驟來建立包含 Adventure Works LT 範例資料的 SQL Database。 

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [SQL Database]。

    ![建立資料庫-1](./media/sql-database-get-started/create-database-1.png)

3. 使用下列資訊填寫 SQL Database 表單，如上圖所示︰ 
   - 資料庫名稱︰使用 **mySampleDatabase**
   - 資源群組︰使用 **myResourceGroup**
   - 來源︰選取 [範例 (AdventureWorksLT)]

4. 按一下 [伺服器] 為您的新資料庫建立及設定新的伺服器。 填寫指定全域唯一伺服器名稱的**新伺服器表單**提供伺服器系統管理員登入的名稱，然後指定您所選擇的密碼。 

    ![建立資料庫伺服器](./media/sql-database-get-started/create-database-server.png)
5. 按一下 [選取] 。

6. 按一下 [定價層] 指定新資料庫的服務層和效能等級。 針對此快速入門，選取 [20 個 DTU] 和 [250] GB 的儲存體

    ![建立資料庫-s1](./media/sql-database-get-started/create-database-s1.png)

7. 按一下 [套用]。  

8. 按一下 [建立] 即可佈建資料庫。 佈建需要幾分鐘的時間。 

9. 在工具列上，按一下 [通知] 以監視部署程序。

    ![通知](./media/sql-database-get-started/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>建立伺服器層級防火牆規則

SQL Database 服務會在伺服器層級建立防火牆，防止外部應用程式和工具連線到伺服器或伺服器上的任何資料庫，除非建立防火牆規則以針對特定的 IP 位址開啟防火牆。 請遵循下列步驟來為您用戶端的 IP 位址建立 [SQL Database 伺服器層級防火牆規則](sql-database-firewall-configure.md)，並讓外部連線僅能夠穿過您 IP 位址的 SQL Database 防火牆。 

1. 部署完成之後，按一下左側功能表中的 [SQL Database]，然後按一下 **SQL Database** 頁面上的新資料庫 **mySampleDatabase**。 資料庫的 [概觀] 頁面隨即開啟，其中會顯示完整伺服器名稱 (例如 **mynewserver20170327.database.windows.net**)，並提供進一步的組態選項。

      ![伺服器防火牆規則](./media/sql-database-get-started/server-firewall-rule.png) 

2. 如先前映像所示，按一下工具列上的 [設定伺服器防火牆]。 SQL Database 伺服器的 [防火牆設定] 頁面隨即開啟。 

3. 依據按一下工具列上的 [新增用戶端 IP] 和 [儲存]。 系統便會為目前的 IP 位址建立伺服器層級防火牆規則。

      ![設定伺服器防火牆規則](./media/sql-database-get-started/server-firewall-rule-set.png) 

4. 依序按一下 [確定] 和 [X] 以關閉 [防火牆設定] 頁面。

您現在可以使用 SQL Server Management Studio 或您選擇的其他工具來連線至資料庫及其伺服器。

## <a name="query-the-sql-database"></a>查詢 SQL Database

當我們建立 SQL Database 時，我們會以 **AdventureWorksLT** 範例資料庫填入它 (這是我們稍早在本快速入門的建立 UI 中所選取的其中一個選項)。 現在讓我們使用 Azure 入口網站的內建查詢工具來查詢資料。 

1. 在資料庫的 [SQL Database] 頁面上，按一下工具列上的 [工具]。 [工具] 頁面隨即開啟。

     ![[工具] 功能表](./media/sql-database-get-started/tools-menu.png) 

2. 依序按一下 [查詢編輯器 (預覽)]、[預覽條款] 核取方塊和 [確定]。 [查詢編輯器] 頁面隨即開啟。

3. 按一下 [登入]，然後在出現提示時選取 [SQL Server 驗證]，接著提供您稍早建立的伺服器管理員登入和密碼。

    ![登入](./media/sql-database-get-started/login.png) 

4. 按一下 [確定] 以登入。

5. 經過驗證後，在查詢編輯器窗格中輸入下列查詢。

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. 按一下 [執行]，然後在 [結果] 窗格中檢閱查詢結果。

    ![查詢編輯器結果](./media/sql-database-get-started/query-editor-results.png)

7. 按一下 [X] 可關閉 [查詢編輯器] 頁面，再次按一下 [X] 可關閉 [工具] 頁面。

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門會建置在本快速入門。 如果您打算繼續進行後續的快速入門或教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源。

1. 從 Azure 入口網站的左側功能表中，依序按一下 [資源群組] 和 [myResourceGroup]。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入 **myResourceGroup**，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

- 若要使用 SQL Server Management Studio 來連線和查詢，請參閱[使用 SSMS 連線及查詢](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio 來連線，請參閱[使用 Visual Studio 連線及查詢](sql-database-connect-query.md)。
- 如需 SQL Database 的技術概觀，請參閱[關於 SQL Database 服務](sql-database-technical-overview.md)。

