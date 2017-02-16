---
title: "SAMW：修正 Azure SQL Database 相容性移轉問題 | Microsoft Docs"
description: "在這篇文章中，您將了解在移轉至 Azure SQL Database 之前，先使用「SQL Azure 移轉精靈」來偵測並修正 SQL Server 資料庫相容性問題時的選項。"
keywords: "Microsoft Azure SQL Database, 資料庫移轉, 資料庫相容性, SQL Azure 移轉精靈"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: bba0893a-3247-406c-b155-acb4b3ef479c
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 006d2d5cd24bc42655bf0b3c0325531144743524


---
# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>在移轉至 Azure SQL Database 之前，先使用「SQL Azure 移轉精靈」來修正 SQL Server 資料庫相容性問題
> [!div class="op_single_selector"]
> * 使用 [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * 使用 [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * 使用 [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
>  

在這篇文章中，您將了解如何在移轉至 Azure SQL Database 之前，先使用「SQL Azure 移轉精靈」來偵測並修正 SQL Server 資料庫相容性問題。

## <a name="using-sql-azure-migration-wizard"></a>使用 SQL Azure 移轉精靈
您可以使用 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/) CodePlex 工具，從不相容的來源資料庫產生 T-SQL 指令碼。 精靈會接著轉換此指令碼，讓它與 SQL Database 相容。 然後您便可以連接到 Azure SQL Database 來執行此指令碼。 此工具也會分析追蹤檔案來判斷相容性問題。 只能使用結構描述產生指令碼，或者指令碼可以 BCP 格式包含資料。 CodePlex 的 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)提供其他文件，包括逐步指引。  

 ![SAMW 移轉圖表](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

> [!NOTE]
> 精靈內建的轉換並不一定能夠修正精靈偵測到的所有不相容結構描述。 系統會將無法處理的不相容指令碼回報為錯誤，並在產生的指令碼中插入註解。 如果偵測到許多錯誤，請使用 Visual Studio 或 SQL Server Management Studio 來逐步執行和修正無法使用 SQL Server 移轉精靈修正的每個錯誤。
> 
> 

## <a name="next-steps"></a>後續步驟
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [將相容 SQL Server 資料庫移轉到 SQL Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他資源
* [SQL Database 功能](sql-database-features.md)
* [Transact-SQL 部分支援或不支援的函數](sql-database-transact-sql-information.md)
* [使用 SQL Server 移轉小幫手來移轉非 SQL Server 資料庫](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


