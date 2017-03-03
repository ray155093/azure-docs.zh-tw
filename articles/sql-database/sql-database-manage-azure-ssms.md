---
title: "使用 SSMS 管理 SQL Database | Microsoft Docs"
description: "了解如何使用 SQL Server Management Studio 管理 SQL Database 伺服器和資料庫。"
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: c5f1ab504bcb639260b2500d462a56ea30cab9aa
ms.lasthandoff: 02/17/2017


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 管理 Azure SQL Database

您可以使用 SQL Server Management Studio (SSMS) 來管理 Azure SQL Database 伺服器和資料庫。 本主題會引導您完成透過 SSMS 執行的常見工作。 在您開始之前，您應該已在 Azure SQL Database 中建立伺服器和資料庫。 如需詳細資訊，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)和[使用 SSMS 進行連接和查詢](sql-database-connect-query-ssms.md)。

> [!TIP]
> 如需示範如何建立伺服器、建立伺服器型防火牆、檢視伺服器屬性、使用 SQL Server Management Studio 進行連接、查詢 Master 資料庫、建立範例資料庫和空白資料庫、查詢資料庫屬性、使用 SQL Server Management Studio 進行連接，以及查詢範例資料庫的教學課程，請參閱[開始使用教學課程](sql-database-get-started.md)。
>

每當您使用 Azure SQL Database 時，建議您使用最新版本的 SSMS。 

> [!IMPORTANT]
> 請一律使用最新版的 SSMS，因為它會持續改進以與最新的 Azure 和 SQL Database 更新搭配運作。 若要取得最新的版本，請參閱 [下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>建立和管理 Azure SQL 資料庫
在已連線到 **master** 資料庫的情況下，您可以在伺服器上建立資料庫，以及修改或卸除現有的資料庫。 下列步驟將說明如何透過 Management Studio 來完成數個常見的資料庫管理工作。 若要執行這些工作，請確保您已使用在設定伺服器時所建立的伺服器層級主體登入，來連線到 [master]  資料庫。

若要在 Management Studio 中開啟查詢視窗，您可以開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

* 使用 **CREATE DATABASE** 陳述式來建立資料庫。 如需詳細資訊，請參閱 [CREATE DATABASE (SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)。 下列陳述式會建立名為 **myTestDB** 的資料庫，並指定它是預設大小上限為 250 GB 的「標準 S0 版本」資料庫。
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

按一下 [執行]  來執行查詢。

* 使用 **ALTER DATABASE** 陳述式來修改現有資料庫，例如，如果您要變更資料庫的名稱或版本。 如需詳細資訊，請參閱 [ALTER DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms174269.aspx)。 下列陳述式會修改您在上一個步驟中建立的資料庫，將版本變更為「標準 S1」。
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* 使用 **the DROP DATABASE** 陳述式來刪除現有的資料庫。 如需詳細資訊，請參閱 [DROP DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx)。 下列陳述式會刪除 **myTestDB** 資料庫，但不會立即卸除此資料庫，因為您將在下一個步驟中使用它來建立登入。
  
      DROP DATABASE myTestBase;
* master 資料庫具有 **sys.databases** 檢視，您可用它來檢視有關所有資料庫的詳細資料。 若要檢視所有現有的資料庫，請執行下列陳述式：
  
      SELECT * FROM sys.databases;
* 在 SQL Database 中， **USE** 陳述式不支援資料庫之間的切換。 相反地，您需要建立與目標資料庫的直接連線。

> [!NOTE]
> 許多可建立或修改資料庫的 Transact-SQL 陳述式必須在自己的批次中執行，且無法與其他 Transact-SQL 陳述式群組在一起。 如需詳細資訊，請參閱陳述式特定資訊。
> 
> 

## <a name="create-and-manage-logins"></a>建立和管理登入
**master** 資料庫包含登入，以及哪些登入具有可建立資料庫或其他登入的權限。 使用在設定伺服器時所建立的伺服器層級主體登入，連線到 **master** 資料庫以管理登入。 您可以使用 **CREATE LOGIN**、**ALTER LOGIN** 或 **DROP LOGIN** 陳述式，來針對管理整個伺服器登入的 master 資料庫執行查詢。 如需詳細資訊，請參閱[管理 SQL Database 中的資料庫和登入](http://msdn.microsoft.com/library/azure/ee336235.aspx)。 

* 使用 **CREATE LOGIN** 陳述式來建立伺服器層級的登入。 如需詳細資訊，請參閱 [CREATE LOGIN (SQL Database)](https://msdn.microsoft.com/library/ms189751.aspx)。 下列陳列式會建立一個名為 **login1**的登入。 使用您選擇的密碼來取代 **password1** 。
  
      CREATE LOGIN login1 WITH password='password1';
* 使用 **CREATE USER** 陳述式來授與資料庫層級的權限。 所有登入都必須在 **master** 資料庫中建立。 若要讓登入能夠連線到不同的資料庫，您必須在該資料庫使用 **CREATE USER** 陳述式來對它授與資料庫層級的權限。 如需詳細資訊，請參閱 [CREATE USER (SQL Database)](https://msdn.microsoft.com/library/ms173463.aspx)。 
* 若要對名為 **myTestDB**的資料庫提供 login1 權限，請完成下列步驟：
  
  1. 若要重新整理「物件總管」以檢視您建立的 **myTestDB** 資料庫，請在「物件總管」中的伺服器名稱上按一下滑鼠右鍵，然後按一下 [重新整理]。  
     
     如果連線已關閉，您可以透過在 [檔案] 功能表上選取 [連接物件總管]  來重新連線。
  2. 在 **myTestDB** 資料庫上按一下滑鼠右鍵，並選取 [新增查詢]。
  3. 針對 myTestDB 資料庫執行下列陳述式，建立名為 **login1User** (對應到伺服器層級登入 **login1**) 的資料庫使用者。
     
         CREATE USER login1User FROM LOGIN login1;
* 使用 **sp\_addrolemember** 預存程序，對使用者帳戶提供資料庫上適當層級的權限。 如需詳細資訊，請參閱 [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx)。 下列陳述式會將 **login1User** 新增到 **db\_datareader** 角色，以將資料庫唯讀權限提供給 **login1User**。
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* 使用 **ALTER LOGIN** 陳述式來修改現有的登入，例如，如果您想要變更登入的密碼。 如需詳細資訊，請參閱 [ALTER LOGIN (SQL Database)](https://msdn.microsoft.com/library/ms189828.aspx)。 **ALTER LOGIN** 陳述式的執行對象應是 **master** 資料庫。 切換回連線到該資料庫的查詢視窗。 下列陳述式會修改 **login1** 登入來重設密碼。 使用您選擇的密碼來取代 **newPassword**，並使用目前的登入密碼來取代 **oldPassword**。
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* 使用 **DROP LOGIN** 陳述式來刪除現有的登入。 刪除伺服器層級的登入也會同時刪除任何相關資料庫使用者帳戶。 如需詳細資訊，請參閱 [DROP DATABASE (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx)。 **DROP LOGIN** 陳述式的執行對象應是 **master** 資料庫。 此陳述式會刪除 **login1** 登入。
  
      DROP LOGIN login1;
* master 資料庫具有 **sys.sql\_logins** 檢視，您可用它來檢視登入。 若要檢視所有現有的登入，請執行下列陳述式：
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>使用動態管理檢視監視 SQL Database
SQL Database 支援數種動態管理檢視，您可用他們來監視個別資料庫。 接下來是一些您可以透過這些檢視擷取的監視資料類型範例。 如需完整的詳細資料和更多使用方式範例，請參閱 [使用動態管理檢視監視 SQL Database](https://msdn.microsoft.com/library/azure/ff394114.aspx)。

* 查詢動態管理檢視需要 **VIEW DATABASE STATE** 權限。 若要將 **VIEW DATABASE STATE** 權限授與特定的資料庫使用者，請連線到資料庫，然後對資料庫執行下列陳述式：
  
      GRANT VIEW DATABASE STATE TO login1User;
* 使用 **sys.dm\_db\_partition\_stats** 檢視來計算資料庫大小。 **sys.dm\_db\_partition\_stats** 檢視會傳回資料庫中每一個資料分割的頁面和資料列計數資訊，您可用這些資訊來計算資料庫大小。 下列查詢會傳回資料庫的大小 (以 MB 為單位)：
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* 使用 **sys.dm\_exec\_connections** 和 **sys.dm\_exec\_sessions** 檢視，來擷取目前使用者連線和資料庫相關內部工作等相關資訊。 下列查詢會傳回目前連接的相關資訊：
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* 使用 **sys.dm\_exec\_query\_stats** 檢視，來擷取快取查詢計劃的彙總效能統計資料。 下列查詢會傳回平均 CPU 時間排名之前五項查詢的相關資訊。
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;


