---
title: "將 Azure SQL 資料庫匯出到 BACPAC 檔案 | Microsoft Docs"
description: "使用 Azure 入口網站將 Azure SQL Database 匯出到 BACPAC 檔案"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2461f706f8fc1150e69312098640c0676206a531
ms.openlocfilehash: 4edd728fe4798450106a4991b353b9dac9de0d88
ms.lasthandoff: 02/17/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>將 Azure SQL 資料庫或 SQL Server 資料庫匯出到 BACPAC 檔案

本文討論將您的 Azure SQL 資料庫或 SQL Server 資料庫匯出到 BACPAC 檔案。 

> [!IMPORTANT]
> Azure SQL 資料庫自動匯出目前為預覽狀態，並會在 2017 年 3 月 1 日停用。 2016 年 12 月 1 日開始，您將無法再於任何 SQL Database 上設定自動匯出。 所有現有的自動匯出作業會繼續運作至 2017 年 3 月 1 日。 2016 年 12 月 1 日之後，您可以使用[長期的備份保留](sql-database-long-term-retention.md)或[Azure 自動化](../automation/automation-intro.md)，根據您選擇的排程使用 PowerShell 定期封存 SQL Database。 如需範例指令碼，您可以[從 GitHub 下載範例指令碼](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)。 
>

## <a name="overview"></a>概觀

當您需要匯出資料庫以封存或移至另一個平台時，您可以將資料庫結構描述和資料匯出到 BACPAC 檔案。 BACPAC 檔案就是副檔名為 BACPAC 的 ZIP 檔案。 BACPAC 檔案可以稍後儲存在 Azure Blob 儲存體，或在內部部署位置的本機儲存體中，之後再匯入至 Azure SQL Database 或 SQL Server 內部部署安裝。 

* 您可以使用 [Azure 入口網站](sql-database-export-portal.md)、[PowerShell](sql-database-export-powershell.md)、[SQLPackage](sql-database-export-sqlpackage.md) 或 [SQL Server Management Studio](sql-database-export-ssms.md)，匯出 Azure SQL 資料庫。
* 您可以使用 [PowerShell](sql-database-export-powershell.md)、[SQLPackage](sql-database-export-sqlpackage.md) 或 [SQL Server Management Studio](sql-database-export-ssms.md) 匯出 SQL Server 資料庫。

> [!IMPORTANT]
> 如果您要從 SQL Server 匯出以準備移轉至 Azure SQL Database，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。
> 

## <a name="considerations"></a>考量

* 為了讓匯出處於交易一致狀態，您必須確定在匯出期間未發生任何寫入活動，或者是從 Azure SQL 資料庫的[交易一致性複本](sql-database-copy.md)匯出。
* 如果您要匯出至 blob 儲存體，BACPAC 檔案的大小上限為 200 GB。 若要封存較大的 BACPAC 檔案，請將匯出到本機儲存體。
* 不支援使用本文所討論的方法將 BACPAC 檔案匯出到 Azure 進階儲存體。
* 如果執行從 Azure SQL Database 匯出的作業超過 20 個小時，它可能會被取消。 若要增加匯出期間的效能，您可以︰
  * 暫時提高您的服務等級。
  * 在匯出期間停止所有讀取及寫入活動。
  * 在所有大型資料表上搭配使用 [叢集索引](https://msdn.microsoft.com/library/ms190457.aspx) 和非 null 值。 若沒有叢集索引，如果要花超過 6-12 小時，匯出可能會失敗。 這是因為匯出服務需要完成資料表掃描，以便嘗試匯出整份資料表。 有一個可判斷資料表是否已針對匯出進行最佳化的好方法，就是執行 **DBCC SHOW_STATISTICS**，並確定 *RANGE_HI_KEY* 不是 null 且其值具有良好的分佈。 如需詳細資料，請參閱 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 並非用於備份和還原作業。 Azure SQL Database 會自動為每個使用者資料庫建立備份。 如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)和 [SQL Database 備份](sql-database-automated-backups.md)。  
> 


## <a name="next-steps"></a>後續步驟

* 如需有關整個 SQL Server 資料庫移轉程序的討論，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。
* 如需在 Azure 內複製資料庫的概觀，請參閱[複製 Azure SQL 資料庫](sql-database-copy.md)。
* 您可以使用 [Azure 入口網站](sql-database-copy-portal.md)、[PowerShell](sql-database-copy-powershell.md)或 [Transact-SQL](sql-database-copy-transact-sql.md)，在 Azure 內複製 Azure SQL 資料庫。 

