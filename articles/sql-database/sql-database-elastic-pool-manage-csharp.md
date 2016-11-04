---
title: 透過 C# 監視和管理彈性資料庫集區 | Microsoft Docs
description: 使用 C# 資料庫開發技術管理 Azure SQL Database 彈性資料庫集區。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein

---
# <a name="monitor-and-manage-an-elastic-database-pool-with-c&#x23;"></a>透過 C&#x23; 監視和管理彈性資料庫集區
> [!div class="op_single_selector"]
> * [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

了解如何使用 C&#x23; 來管理[彈性資料庫集區](sql-database-elastic-pool.md)。 

> [!NOTE]
> SQL Database 的許多新功能只有在使用 [Azure Resource Manager 部署模型](../resource-group-overview.md)時才支援，所以您應該一律使用最新的**適用於 .NET ([docs](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)) 的 Azure SQL Database Management Library**。 支援較舊的[以傳統部署模型為基礎的程式庫](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)，以提供回溯相容性，因此我們建議您使用較新的以 Resource Manager 為基礎的程式庫。
> 
> 

若要完成這篇文章中的步驟，您需要下列項目︰

* 一個彈性集區 (您想要管理的集區)。 若要建立集區，請參閱[使用 C# 建立彈性資料庫集區](sql-database-elastic-pool-create-csharp.md)。
* 。 如需免費的 Visual Studio，請參閱 [Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs) 頁面。

## <a name="move-a-database-into-an-elastic-pool"></a>將資料庫移入彈性集區
您可以將獨立資料庫移入或移出集區。  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>列出彈性集區中的資料庫
若要擷取集區中的所有資料庫，請呼叫 [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) 方法。

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>變更集區的效能設定
擷取現有的集區屬性。 修改值，然後執行 CreateOrUpdate 方法。

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>彈性集區的作業延遲
* 每個資料庫的最小 eDTU 數或每個資料庫的最大 eDTU 數變更作業通常在 5 分鐘內即可完成。
* 變更集區大小的時間 (eDTUs) 取決於集區中所有資料庫的合併大小。 變更作業平均每 100 GB 會在 90 分鐘以內完成。 舉例來說，如果集區中所有資料庫的總空間為 200 GB，則每集區變更集區 eDTU 的預期延遲時間會少於 3 小時。

## <a name="additional-resources"></a>其他資源
* [SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題](sql-database-develop-error-messages.md)。
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
* [Azure 資源管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* [使用 C# 建立新的彈性資料庫集區](sql-database-elastic-pool-create-csharp.md)
* [何時使用彈性資料庫集區？](sql-database-elastic-pool-guidance.md)
* 請參閱 [使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)︰使用彈性資料庫工具相應放大、移動資料、查詢或建立交易。

<!--HONumber=Oct16_HO2-->


