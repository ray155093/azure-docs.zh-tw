---
title: "SqlPackage：從 BACPAC 檔案匯入到 Azure SQL Database | Microsoft Docs"
description: "本文說明如何使用 SqlPackage 命令列公用程式，從 BACPAC 檔案匯入到 SQL Database。"
keywords: "Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 匯入 BACPAC 檔案, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>使用 SqlPackage 將資料庫從 BACPAC 檔案匯入 Azure SQL Database

本文說明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式，從 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案匯入 SQL 資料庫。 此公用程式隨附於最新版的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，或者您也可以直接從 Microsoft 下載中心下載最新版的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

> [!NOTE]
> 下列步驟假設您已佈建 SQL Database 伺服器、手上有連接資訊並已確認來源資料庫相容。
> 
> 

## <a name="import-the-database"></a>匯入資料庫
使用 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式，從 BACPAC 檔案匯入相容的 SQL Server 資料庫 (或 Azure SQL 資料庫)。

> [!NOTE]
> 下列步驟假設您已佈建 Azure SQL Database 伺服器並且手邊有連接資訊。
>  

透過命令提示字元，在包含最新版 sqlpackage.exe 命令列公用程式的目錄中，執行類似下列範例命令的命令，將 BACPAC 檔案匯入 Azure SQL Database 中成為進階 P11。

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>後續步驟

* 如需有關整個 SQL Server 資料庫移轉程序的討論，包括效能建議，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。
* 如需 SQLPackage 的參考內容，請參閱 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)。
* 若要下載最新版的 SQLPackage，請參閱 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。
* 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。




<!--HONumber=Feb17_HO2-->


