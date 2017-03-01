---        
title: "探索 Azure SQL Database 教學課程 | Microsoft Docs"
description: "了解 SQL Database 特性與功能"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 9b09383350284c8f1cd3e384c802c25c962b1226
ms.openlocfilehash: 1fe15e7ad3667d42995cd487c793fae496216fca
ms.lasthandoff: 02/16/2017

        
---
 
# <a name="explore-azure-sql-database-tutorials"></a>探索 Azure SQL Database 教學課程
下表中的連結會帶您前往每個所列功能區域的概觀，以及每個區域的簡單逐步教學課程。 

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>建立伺服器、資料庫和伺服器層級防火牆規則
在下列教學課程中，您將會建立伺服器、資料庫和伺服器層級防火牆規則，並了解如何連接及查詢伺服器和資料庫。

| 教學課程 | 說明 |
| --- | --- | 
| [您的第一個 Azure SQL Database](sql-database-get-started.md) | 完成本快速入門教學課程之後，您將會有在 Azure 資源群組中執行並已附加至邏輯伺服器的範例資料庫和空白資料庫。 您也會有兩個伺服器層級防火牆規則，其設定為可啟用伺服器層級主體以從兩個指定的 IP 位址登入伺服器。 最後，您會了解如何在 Azure 入口網站查詢資料庫，以及如何使用 SQL Server Management Studio 來連接和查詢。 |
| [使用 PowerShell 佈建及存取 Azure SQL Database](sql-database-get-started-powershell.md) | 當完成本教學課程時，您將會有在 Azure 資源群組中執行並已附加至邏輯伺服器的範例資料庫和空白資料庫。 您也會有伺服器層級防火牆規則，設定為可啟用伺服器層級主體以從指定的 IP 位址 (或 IP 位址範圍) 登入伺服器。 |
| [以 SQL Database Library for .NET 使用 C# 建立 SQL 資料庫](sql-database-get-started-csharp.md)| 在本教學課程中，您會使用 C# 來建立 SQL Database 伺服器、防火牆規則和 SQL 資料庫。 您也可以建立 Active Directory (AD) 應用程式，以及驗證 C# 應用程式所需的服務主體。 |
|  | |

## <a name="backups-long-term-retention-and-database-recovery"></a>備份、長期保留和資料庫復原
在下列教學課程中，您將了解如何使用[資料庫備份](sql-database-automated-backups.md)、[長期備份保留](sql-database-long-term-retention.md)和[使用備份進行資料庫復原](sql-database-recovery-using-backups.md)。

| 教學課程 | 說明 |
| --- | --- | 
| [使用 Azure 入口網站進行備份與還原](sql-database-get-started-backup-recovery-portal.md) | 在本教學課程中，您將了解如何使用 Azure 入口網站來檢視備份、還原至某個時間點、設定長期備份保存，以及從 Azure 復原服務保存庫中的備份還原。
| [使用 PowerShell 進行備份與還原](sql-database-get-started-backup-recovery-powershell.md) | 在本教學課程中，您將了解如何使用 PowerShell 來檢視備份、還原至某個時間點、設定長期備份保存，以及從 Azure 復原服務保存庫中的備份還原。
|  | |

## <a name="sharded-databases"></a>分區化資料庫
在下列教學課程中，您將了解如何[使用分區對應管理員相應放大資料庫](sql-database-elastic-scale-shard-map-management.md)。

| 教學課程 | 說明 |
| --- | --- | 
| [建立分區化應用程式](sql-database-elastic-scale-get-started.md) |在本教學課程中，您將了解如何利用簡單的分區化應用程式來使用彈性資料庫工具的功能。 |
| [部署分割合併服務](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |在本教學課程中，您將了解如何在分區化資料庫之間移動資料。 |
|  | |

## <a name="elastic-database-jobs"></a>彈性資料庫工作
在下列教學課程中，您將了解如何使用[彈性資料庫作業](sql-database-elastic-jobs-overview.md)。

| 教學課程 | 說明 |
| --- | --- | 
| [開始使用 Azure SQL Database 彈性工作](sql-database-elastic-jobs-getting-started.md) |在本教學課程中，您將了解如何建立及管理用來管理一組相關資料庫的作業。 |
|  | |

## <a name="elastic-queries"></a>彈性查詢
在下列教學課程中，您將了解如何執行[彈性查詢](sql-database-elastic-query-overview.md)。

| 教學課程 | 說明 |
| --- | --- | 
| [建立彈性查詢](sql-database-elastic-query-getting-started-vertical.md) | 在本教學課程中，您將了解如何使用單一連接點執行跨多個資料庫的 T-SQL 查詢 |
| [所有相應放大之雲端資料庫的報告](sql-database-elastic-query-getting-started.md) |在本教學課程中，您將了解如何從水平分割 (分區化) 資料庫中的所有資料庫建立報告 |
| [對不同結構描述的雲端資料庫執行查詢](sql-database-elastic-query-vertical-partitioning.md) | 在本教學課程中，您將了解如何對具有不同結構描述的多個資料庫執行 T-SQL 查詢 |
| [跨相應放大的雲端資料庫報告](sql-database-elastic-query-horizontal-partitioning.md) |在本教學課程中，您將了解如何建立跨分區化資料庫中所有資料庫的報告。 |
|  | |

## <a name="database-authentication-and-authorization"></a>資料庫驗證和授權
在下列教學課程中，您將了解如何[建立和管理登入與使用者](sql-database-manage-logins.md)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [SQL 驗證和授權](sql-database-control-access-sql-authentication-get-started.md) | 您會在本教學課程中學習如何使用 SQL Server 驗證來建立登入和使用者，然後將他們加入角色並授與權限 |
| [Azure AD 驗證和授權](sql-database-control-access-aad-authentication-get-started.md) | 您會在本教學課程中學習如何使用 Azure Active Directory 驗證來建立登入和使用者 |
|  | |

## <a name="secure-and-protect-data"></a>防全和保護資料
在下列教學課程中，您將了解如何[保護 Azure SQL Database 資料](sql-database-security-overview.md)。

| 教學課程 | 說明 |
| --- | --- | 
| [使用「永遠加密」保護機密資料 ](sql-database-always-encrypted-azure-key-vault.md) |在本教學課程中，您將使用「永遠加密」精靈保護 Azure SQL 資料庫中的機密資料。 |
|  | |

## <a name="develop"></a>開發
在下列教學課程中，您將了解應用程式和資料庫開發。

| 教學課程 | 說明 |
| --- | --- | 
| [使用 Excel 建立報告](sql-database-connect-excel.md) |在本教學課程中，您將了解如何將 Excel 連接至雲端的 SQL 資料庫，以便匯入資料並根據資料庫中的值來建立資料表和圖表。 |
| [使用 SQL Server 建置應用程式](https://www.microsoft.com/sql-server/developer-get-started/) |在本教學課程中，您將了解如何使用 SQL Server 建置應用程式 |
| [暫存資料表](sql-database-temporal-tables.md) | 在本教學課程中，您將了解暫存資料表。
| [使用 Entity Framework 搭配彈性工具](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |在本教學課程中，您將了解 Entity Framework 應用程式為了要與彈性資料庫工具整合所需做的變更。 |
| [採用記憶體內部 OLTP](sql-database-in-memory-oltp-migration.md) | 在本教學課程中，您將了解如何使用[記憶體中 OLTP](sql-database-in-memory.md) 來改善交易處理的效能。 |
| [新資料庫的 Code First](https://msdn.microsoft.com/data/jj193542.aspx) | 在本教學課程中，您將了解 Code First 部署。
| [Tailspin Surveys 範例應用程式](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) | 在本教學課程中，您會使用 Tailspon Surveys 範例應用程式。 |
| [Contoso 診所示範應用程式](https://github.com/Microsoft/azure-sql-security-sample) | 在本教學課程中，您會使用 Contoso 診所示範應用程式。 |
|  | |

## <a name="data-sync"></a>資料同步
在本教學課程中，您將了解[資料同步](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)。

| 教學課程 | 說明 |
| --- | --- | 
| [開始使用 Azure SQL 資料同步 (預覽)](sql-database-get-started-sql-data-sync.md) |在本教學課程中，您將了解使用 Azure 傳統入口網站的 Azure SQL 資料同步基本概念。 |
|  | |

## <a name="monitor-and-tune"></a>監視與微調
在下列教學課程中，您將了解監視和微調。
| 教學課程 | 說明 |
| --- | --- | 
| [使用 PowerShell 進行彈性集區遙測](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)| 在本教學課程中，您將了解如何使用 PowerShell 收集彈性集區遙測。 |
| [SaaS 的彈性集區自訂儀表板](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) | 在本教學課程中，您將了解如何建立用於監視彈性集區的自訂儀表板。 |
| [將擴充的活動擷取至活動檔案目標](sql-database-xevent-code-event-file.md)| 在本教學課程中，您將了解如何將擴充的活動擷取至活動目標檔案。|
| [將擴充的活動擷取至信號緩衝區目標](sql-database-xevent-code-ring-buffer.md)| 在本教學課程中，您將了解如何將擴充的活動擷取至代碼信號緩衝區。|
|  | |



