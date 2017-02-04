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
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0b200aff6a38a76abd1abaa742006f3d75e7121e
ms.openlocfilehash: 0cbde2e33517e46e49824d9f66f75e4e2f384d97


---
# <a name="explore-azure-sql-database-tutorials"></a>探索 Azure SQL Database 教學課程
下列連結會帶您前往每個所列功能區域的概觀，以及每個區域的簡單逐步教學課程。 如需以解決方案為範圍、根據真實世界案例示範在完整解決方案中如何使用 SQL Database 的快速入門，請參閱 [Azure SQL Database 解決方案快速入門](sql-database-solution-quick-starts.md)。

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio
在下列教學課程中，您將會了解如何使用 SQL Server Management Studio 來管理及查詢 Azure SQL Database。

> [!IMPORTANT]
> 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
>

| 教學課程 | 說明 |
| --- | --- | --- |
| [使用伺服器層級主體登入來連接到 SQL Database](sql-database-get-started-security.md#connect-to-azure-sql-database-using-sql-server-authentication) |在本教學課程中，您將了解如何使用 SQL 伺服器層級主體登入連線到 Azure SQL Database。 |
| [以使用者身分連接到 Azure SQL Database](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) |在本教學課程中，您將了解如何使用資料庫層級使用者帳戶連線到 Azure SQL Database。 |
|  | |

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>建立伺服器、資料庫和伺服器層級防火牆規則
在下列教學課程中，您將會建立伺服器、資料庫和伺服器層級防火牆規則，並了解如何連接及查詢伺服器和資料庫。

| 教學課程 | 說明 |
| --- | --- | --- |
| [藉由使用 Azure 入口網站和 SQL Server Management Studio 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started.md) | 在本教學課程中，您會使用 Azure 入口網站和 SQL Server Management Studio 來建立 Azure SQL Database 邏輯伺服器、檢視其屬性、建立伺服器層級防火牆、連接至 Master 資料庫、建立範例資料庫、檢視其屬性、連接到範例資料庫，以及建立空白資料庫。 |
| [使用 Azure PowerShell 來開始使用 Azure SQL Database 伺服器、資料庫和防火牆規則](sql-database-get-started-powershell.md) | 在本教學課程中，您會使用 PowerShell 來建立 Azure SQL Database 邏輯伺服器、檢視其屬性、建立伺服器層級防火牆、連接至 Master 資料庫、建立範例資料庫、檢視其屬性、連接到範例資料庫，以及建立空白資料庫。 |
| [以 SQL Database Library for .NET 使用 C# 建立 SQL 資料庫](sql-database-get-started-csharp.md)| 在本教學課程中，您會使用 C# 來建立 SQL Database 伺服器、防火牆規則和 SQL 資料庫。 您也可以建立 Active Directory (AD) 應用程式，以及驗證 C# 應用程式所需的服務主體。 |

## <a name="backup-and-recovery"></a>備份與復原
在下列教學課程中，您會將資料庫還原到某個時間點、設定長期的備份保存，以及從 Azure 復原服務保存庫中的保留還原資料庫。 

| 教學課程 | 說明 |
| --- | --- | --- |
| [開始使用備份與還原以保護和修復資料](sql-database-get-started-backup-recovery.md) | 在本教學課程中，您會使用 Azure 入口網站，將資料庫還原到某個時間點、設定長期的備份保存，以及從 Azure 復原服務保存庫中的保留還原資料庫。 |


## <a name="elastic-pools"></a>彈性集區
在下列教學課程中，您將了解如何使用 [彈性集區](sql-database-elastic-pool.md) ，針對使用模式顯著不同且無法預測的多個資料庫，管理其效能目標。

| 教學課程 | 說明 |
| --- | --- | --- |
| [建立彈性集區](sql-database-elastic-pool-create-portal.md) |在本教學課程中，您將了解如何建立可調整的 Azure SQL Database 集區。 |
| [監視彈性資料庫](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) |在本教學課程中，您將了解如何針對潛在的問題監視個別彈性資料庫。 |
| [將警示加入集區資源](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) |在本教學課程中，您將了解如何將規則加入資源，以在當資源達到您設定的使用率閾值時，傳送電子郵件給人員或傳送警示字串到 URL 端點。 |
| [將資料庫移入彈性集區](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) |在本教學課程中，您將了解如何將資料庫移入彈性集區。 |
| [將資料庫移出彈性集區](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) |在本教學課程中，您將了解如何將資料庫移出彈性集區。 |
| [變更集區的效能設定](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) |在本教學課程中，您將了解如何調整集區的效能和儲存體限制。 |
|  | |

## <a name="elastic-database-jobs"></a>彈性資料庫工作
在下列教學課程中，您將了解如何使用 [彈性資料庫工作](sql-database-elastic-jobs-overview.md)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [開始使用彈性資料庫工具](sql-database-elastic-scale-get-started.md) |在本教學課程中，您將了解如何利用簡單的分區化應用程式來使用彈性資料庫工具的功能。 |
| [開始使用 Azure SQL Database 彈性工作](sql-database-elastic-jobs-getting-started.md) |在本教學課程中，您將了解如何建立及管理用來管理相關資料庫群組的工作。 |
|  | |

## <a name="elastic-queries"></a>彈性查詢
在下列教學課程中，您將了解如何執行 [彈性查詢](sql-database-elastic-query-overview.md)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [查詢跨水平資料分割 (分區化) 資料庫](sql-database-elastic-query-getting-started.md) |在本教學課程中，您將了解如何使用 [彈性查詢](sql-database-elastic-query-overview.md) |
| [查詢跨垂直資料分割資料庫)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) |在本教學課程中，您將了解如何使用 [彈性查詢](sql-database-elastic-query-overview.md) |
| [將現有的資料庫移轉到相應放大的資料庫](sql-database-elastic-convert-to-use-elastic-tools.md) |在本教學課程中，您將了解如何水平調整 (分區) Azure SQL Database。 |
|  | |

## <a name="performance-optimization"></a>效能最佳化
在下列教學課程中，您將了解如何將[獨立資料庫的效能](sql-database-performance-guidance.md)最佳化。 如需了解如何將多個資料庫的效能最佳化，請參閱 [彈性集區](#elastic-pools)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [變更您資料庫的服務層級和效能等級](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) |在本教學課程中，您將了解如何使用服務層相應增加或相應減少 Azure SQL Database 的效能。 |
| [SQL Database 建議程式查詢效能深入解析](sql-database-performance.md#performance-overview) |在本教學課程中，您將了解如何開啟和使用 SQL Database 建議程式查詢效能深入解析。 |
| [SQL Database 建議程式效能建議](sql-database-advisor.md) |在本教學課程中，您將了解如何檢視並套用 SQL Database 建議程式效能建議。 |
| [檢閱排名最前面的 CPU 取用查詢](sql-database-query-performance.md#review-top-cpu-consuming-queries) |在本教學課程中，您將了解如何使用 SQL Database 建議程式查詢效能深入解析來檢閱排名最前面的 CPU 取用查詢。 |
| [檢視個別查詢詳細資料](sql-database-query-performance.md#viewing-individual-query-details) |在本教學課程中，您將了解如何使用 SQL Database 建議程式查詢效能深入解析以檢視個別查詢效能詳細資料。 |
|  | |

## <a name="sql-database-migration-and-archive"></a>SQL Database 移轉與封存
在下列教學課程中，您將了解如何 [將現有的 SQL Server 資料庫移轉到 Azure SQL Database](sql-database-cloud-migrate.md)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [使用 SQL Server Data Tools for Visual Studio 偵測相容性問題](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |在本教學課程中，您將了解如何使用 Visual Studio 的 SQL Server Data Tools 來判斷 Azure SQL Database 相容性。 |
| [使用 SQL Server Data Tools for Visual Studio 修正相容性問題](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |在本教學課程中，您將了解如何使用 Visual Studio 的 SQL Server Data Tools 來修正 Azure SQL Database 相容性問題。 |
| [使用 SqlPackage.exe 判斷 SQL Database 相容性](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) |在本教學課程中，您將了解如何使用 SQLPackage.exe 命令列公用程式來判斷 Azure SQL Database 相容性。 |
| [使用 SSMS 判斷 SQL Database 相容性](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |在本教學課程中，您將了解如何使用 SQL Server Management Studio 來判斷 Azure SQL Database 相容性。 |
| [使用「將資料庫部署到 Microsoft Azure Database 精靈」將 SQL Database 移轉到 SQL Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) |在本教學課程中，您將了解如何使用 SQL Server Management Studio 中的「部署資料庫到 Microsoft Azure Database 精靈」來將相容的 SQL Server 資料庫移轉到 Azure SQL Database。 |
| [使用 SSMS 將 SQL Server Database 匯出到 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) |在本教學課程中，您將了解如何使用 SQL Server Management Studio 中的「匯出資料層應用程式精靈」將相容的 SQL Server 資料庫匯出成 BACPAC 檔案。 |
| [使用 SqlPackage 將的 SQL Server Database 匯出到 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) |在本教學課程中，您將了解如何使用 SQLPackage.exe 命令列公用程式將相容的 SQL Server 資料庫匯出成 BACPAC 檔案。 |
| [使用 SSMS 將 BACPAC 檔案匯入 Azure SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) |在本教學課程中，您將了解如何使用 SQL Server Management Studio 中的「匯出資料層應用程式精靈」從 BACPAC 檔案將資料庫匯入 Azure SQL Database。 |
| [使用 SqlPackage 將 BACPAC 檔案匯入 Azure SQL Database](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) |在本教學課程中，您將了解如何使用 SQLPackage.exe 命令列公用程式從 BACPAC 檔案將資料庫匯入 Azure SQL Database。 |
| [使用 Azure 入口網站將 BACPAC 檔案匯入 Azure SQL Database](sql-database-import.md) |在本教學課程中，您將了解如何使用 Azure 入口網站從儲存在 Azure Blob 中的 BACPAC 檔案將資料庫匯入 Azure SQL Database。 |
| [使用 PowerShell 將 BACPAC 檔案匯入 Azure SQL Database](sql-database-import-powershell.md) |在本教學課程中，您將了解如何使用 PowerShell 從 BACPAC 檔案將資料庫匯入 Azure SQL Database。 |
| [使用 Azure 入口網站封存 Azure SQL Database](sql-database-export.md#export-your-database) |在本教學課程中，您將了解如何使用 Azure 入口網站將 Azure SQL Database 封存到 BACPAC 檔案。 |
| [使用 PowerShell 封存 Azure SQL Database](sql-database-export-powershell.md) |在本教學課程中，您將了解如何使用 PowerShell 將 Azure SQL Database 封存到 BACPAC 檔案。 |
| [使用 Azure 入口網站複製 Azure SQL Database](sql-database-copy.md) |在本教學課程中，您將了解如何使用 Azure 入口網站複製 Azure SQL Database。 |
| [使用 PowerShell 複製 Azure SQL Database](sql-database-copy-powershell.md) |在本教學課程中，您將了解如何使用 PowerShell 複製 Azure SQL Database。 |
| [使用 Transact-SQL 複製 Azure SQL Database](sql-database-copy-transact-sql.md#copy-your-sql-database) |在本教學課程中，您將了解如何使用 Transact-SQL 複製 Azure SQL Database。 |
|  | |

## <a name="develop"></a>開發
在下列教學課程中，您將了解 [SQL Database 開發](sql-database-develop-overview.md)和如何使用[連線庫](sql-database-libraries.md)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [使用 .NET (C#) 連接到 SQL Database](sql-database-develop-dotnet-simple.md) |在本教學課程中，您將了解如何使用 C# 連接到 Azure SQL Database。 |
| [使用 Java 連接到 SQL Database](sql-database-develop-java-simple.md) |在本教學課程中，您將了解如何使用 Java 連接到 Azure SQL Database。 |
| [使用 Node.js 連接到 SQL Database](sql-database-develop-nodejs-simple.md) |在本教學課程中，您將了解如何使用 Node.js 連接到 Azure SQL Database。 |
| [使用 PHP 連接到 SQL Database](sql-database-develop-php-simple.md) |在本教學課程中，您將了解如何使用 PHP 連接到 Azure SQL Database。 |
| [使用 Python 連接到 SQL Database](sql-database-develop-python-simple.md) |在本教學課程中，您將了解如何使用 Python 連接到 Azure SQL Database。 |
| [使用 Ruby 連接到 SQL Database](sql-database-develop-ruby-simple.md) |在本教學課程中，您將了解如何使用 Ruby 連接到 Azure SQL Database。 |
|  | |

## <a name="database-access"></a>資料庫存取
在下列教學課程中，您將了解如何 [建立和管理登入與使用者](sql-database-manage-logins.md)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [使用 Azure 入口網站建立 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md) |在本教學課程中，您將了解如何使用 Azure 入口網站設定 SQL Database 伺服器層級防火牆。 |
| [使用 Transact-SQL 建立資料庫層級防火牆規則](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) |在本教學課程中，您將了解如何使用 Transact-SQL 建立資料庫層級防火牆規則。 |
| [使用 Transact-SQL 管理伺服器層級防火牆規則](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules) |在本教學課程中，您將了解如何使用 Transact-SQL 管理 Azure SQL Database 伺服器層級防火牆。 |
| [使用 PowerShell 管理伺服器層級防火牆規則](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-by-using-powershell) |在本教學課程中，您將了解如何使用 PowerShell 管理 Azure SQL Database 伺服器層級防火牆。 |
| [使用 REST API 管理伺服器層級防火牆規則](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-rest-api) |在本教學課程中，您將了解如何使用 REST API 管理 Azure SQL Database 伺服器層級防火牆。 |
| [使用伺服器層級主體登入來連接到 SQL Database](sql-database-get-started-security.md#connect-to-azure-sql-database-using-sql-server-authentication) |在本教學課程中，您將了解如何使用伺服器層級主體登入連線到 Azure SQL Database。 |
| [授與登入資料庫存取權](sql-database-manage-logins.md#granting-database-access-to-a-login() |在本教學課程中，您將了解如何授與伺服器層級登入資料庫存取權。 |
| [使用 SSMS 建立新的資料庫使用者](sql-database-get-started-security.md#create-new-database-user-using-ssms) |在本教學課程中，您將了解如何使用 SSMS 在現有資料庫中建立新的資料庫使用者。 |
| [授與新的資料庫使用者 db_owner 權限](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) |在本教學課程中，您將了解如何授與現有的資料庫使用者 db_owner 權限。 |
| [以使用者身分連接到 Azure SQL Database](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) |在本教學課程中，您將了解如何使用資料庫層級使用者帳戶連線到 Azure SQL Database。 |
|  | |

## <a name="data-security"></a>資料安全性
在下列教學課程中，您將了解如何 [保護 Azure SQL Database 資料](sql-database-security.md)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [使用 Azure 入口網站為資料庫啟用威脅偵測](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) |在本教學課程中，您將了解如何在 Azure 入口網站中為您的資料庫設定威脅偵測。 |
| [使用「永遠加密」保護機密資料 ](sql-database-always-encrypted-azure-key-vault.md) |在本教學課程中，您將使用「永遠加密」精靈保護 Azure SQL Database 中的機密資料。 |
| [使用透明資料加密保護機密資料](https://msdn.microsoft.com/library/dn948096.aspx) |在本教學課程中，您將了解如何使用透明資料加密來保護機密資料。 |
| [加密資料行](https://msdn.microsoft.com/library/ms179331.aspx) |在本教學課程中，您將了解如何使用 Transact-SQL 加密資料行。 |
| [設定動態資料遮罩](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) |在本教學課程中，您將了解如何為您的 Azure SQL Database 設定動態資料遮罩。 |
|  | |

## <a name="business-continuity-and-query-scale-out"></a>商務持續性和向外延展查詢
在下列教學課程中，您將了解如何使用 [異地復原和作用中異地複寫](sql-database-business-continuity.md) 來從錯誤復原，以提供商務持續性及向外延展查詢。

| 教學課程 | 說明 |
| --- | --- | --- |
| [使用 Azure 入口網站將 Azure SQL Database 還原至先前的時間點](sql-database-point-in-time-restore.md) |在本教學課程中，您將了解如何使用 Azure 入口網站將資料庫還原至先前的時間點。 |
| [使用 PowerShell 將 Azure SQL Database 還原至先前的時間點](sql-database-point-in-time-restore.md) |在本教學課程中，您將了解如何使用 PowerShell 將資料庫還原至先前的時間點。 |
| [還原使用 Azure 入口網站的已刪除 Azure SQL Database](sql-database-restore-deleted-database-portal.md) |在本教學課程中，您將了解如何使用 Azure 入口網站還原已刪除的資料庫。 |
| [使用 PowerShell 還原已刪除的 Azure SQL Database](sql-database-restore-deleted-database-powershell.md) |在本教學課程中，您將了解如何使用 PowerShell 還原已刪除的資料庫。 |
| [使用 Azure 入口網站為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-portal.md) |在本教學課程中，您將了解如何使用 Azure 入口網站來設定「作用中異地複寫」。 |
| [使用 PowerShell 為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-powershell.md) |在本教學課程中，您將了解如何使用 PowerShell 來設定「作用中異地複寫」。 |
| [使用 Transact-SQL 為 Azure SQL Database 設定異地複寫](sql-database-geo-replication-transact-sql.md) |在本教學課程中，您將了解如何使用 Transact-SQL 來設定「作用中異地複寫」。 |
| [使用 Azure 入口網站為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-portal.md) |在本教學課程中，您將了解如何使用 Azure 入口網站容錯移轉至異地複寫的次要複本。 |
| [使用 PowerShell 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-powershell.md) |在本教學課程中，您將了解如何使用 PowerShell 容錯移轉至異地複寫的次要複本。 |
| [使用 Transact-SQL 為 Azure SQL Database 起始計劃性或非計劃性容錯移轉](sql-database-geo-replication-failover-transact-sql.md) |在本教學課程中，您將了解如何使用 Transact-SQL 容錯移轉至異地複寫的次要複本。 |
|  | |

## <a name="data-sync"></a>資料同步
在本教學課程中，您將了解 [資料同步](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)。

| 教學課程 | 說明 |
| --- | --- | --- |
| [開始使用 Azure SQL 資料同步 (預覽)](sql-database-get-started-sql-data-sync.md) |在本教學課程中，您將了解使用 Azure 傳統入口網站的 Azure SQL 資料同步基本概念。 |
|  | |

## <a name="next-steps"></a>後續步驟
[探索 Azure SQL Database 解決方案快速入門](sql-database-solution-quick-starts.md)



<!--HONumber=Dec16_HO2-->


