---
title: "使用 Azure SQL Database 進行管理與開發的工具 | Microsoft Docs"
description: "介紹 Azure SQL Database 的管理與開發工具和選項"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 9b0a3a314e576db2133e5c63fada43bb11a4e520
ms.contentlocale: zh-tw
ms.lasthandoff: 03/04/2017


---
# <a name="overview-tools-to-manage--develop-with-azure-sql-database"></a>概觀︰使用 Azure SQL Database 進行管理與開發的工具
本主題說明使用 Azure SQL Database 進行管理與開發的工具。

> [!IMPORTANT]
> 此文件集包括快速入門、範例及操作說明指南，示範如何使用下列段落中所介紹的工具來使用 Azure SQL Database 進行管理與開發。 使用左側導覽窗格和篩選方塊來尋找 Azure 入口網站、PowerShell 和 T-SQL 的特定內容。
>

## <a name="azure-portal"></a>Azure 入口網站
[Azure 入口網站](https://portal.azure.com) 是網頁式應用程式，您可以在其中建立、更新和刪除資料庫和邏輯伺服器，以及監視資料庫活動。 如果您剛開始使用 Azure、管理幾個資料庫，或是需要快速執行一些工作，此工具便相當適合您。

## <a name="sql-server-management-studio-and-transact-sql"></a>SQL Server Management Studio 和 Transact-SQL
SQL Server Management Studio (SSMS) 是一種用戶端工具，可在您的電腦上執行，以使用 Transact-SQL 管理您的雲端資料庫。 許多資料庫系統管理員都熟悉可和 Azure SQL Database 搭配使用的 SSMS。 [下載最新版的 SSMS](https://msdn.microsoft.com/library/mt238290) ，並一律以最新版和 Azure SQL Database 搭配使用。 

> [!IMPORTANT]
> 請一律使用最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290)。
>  

## <a name="sql-server-data-tools-in-visual-studio"></a>Visual Studio 中的 SQL Server Data Tools
SQL Server Data Tools (SSDT) 是一種用戶端工具，可在您的電腦上執行，以開發您的雲端資料庫。 如果您是熟悉 Visual Studio 或其他整合式開發環境 (IDE) 的應用程式開發人員， [請嘗試使用 Visual Studio 中的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)。  

> [!IMPORTANT]
> 請一律使用最新版本的 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) 以便與 Microsoft Azure 和 SQL Database 更新保持同步。
>  
## <a name="powershell"></a>PowerShell
您可以使用 PowerShell 來管理資料庫和彈性集區，以及自動執行 Azure 資源部署。 若您要在生產環境中管理大量資料庫並自動化部署和資源變更，Microsoft 建議使用這項工具。

## <a name="elastic-database-tools"></a>彈性資料庫工具
使用彈性資料庫工具執行動作，例如 

* 使用 [彈性工作](sql-database-elastic-jobs-overview.md)
* 使用 [分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)
* 使用 [Elastic Scale 用戶端程式庫](sql-database-elastic-database-client-library.md)，管理單一租用戶模型或多租用戶模型中的資料庫。

## <a name="additional-resources"></a>其他資源
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Azure 自動化](https://azure.microsoft.com/documentation/services/automation/)
* [Azure 排程器](https://azure.microsoft.com/documentation/services/scheduler/)


