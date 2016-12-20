---
title: "概觀：SQL Database 的管理工具 | Microsoft Docs"
description: "比較管理 Azure SQL Database 的工具和選項"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5adb83a7b8e19ca78dd4d837b497e1ceb4444f86


---
# <a name="overview-management-tools-for-sql-database"></a>概觀：SQL Database 的管理工具
本主題將探索並比較管理 Azure SQL 資料庫的工具和選項，讓您可以挑選適合您工作、業務和您自身的正確工具。 要選擇正確的工具就必須考量到您管理多少資料庫、工作本身以及執行工作的頻率。

## <a name="azure-portal"></a>Azure 入口網站
[Azure 入口網站](https://portal.azure.com) 是網頁式應用程式，您可以在其中建立、更新和刪除資料庫和邏輯伺服器，以及監視資料庫活動。 如果您剛開始使用 Azure、管理幾個資料庫，或是需要快速執行一些工作，此工具便相當適合您。

如需有關使用入口網站的詳細資訊，請參閱[使用 Azure 入口網站管理 SQL Database](sql-database-manage-portal.md)。

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>Visual Studio 中的 SQL Server Management Studio 和 SQL Server Data Tools
SQL Server Management Studio (SSMS) 和 SQL Server Data Tools (SSDT) 是在您電腦上執行的用戶端工具，可用來在雲端管理和開發您的資料庫。 如果您是熟悉 Visual Studio 或其他整合式開發環境 (IDE) 的應用程式開發人員， [請嘗試使用 Visual Studio 中的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)。 許多資料庫系統管理員都熟悉可和 Azure SQL Database 搭配使用的 SSMS。 [下載最新版的 SSMS](https://msdn.microsoft.com/library/mt238290) ，並一律以最新版和 Azure SQL Database 搭配使用。 如需使用 SSMS 管理 Azure SQL Database 的詳細資訊，請參閱 [使用 SSMS 管理 SQL Database](sql-database-manage-azure-ssms.md)。

> [!IMPORTANT]
> 請一律使用最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) 和 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)，以與 Microsoft Azure 及 SQL Database 的更新保持同步。
>  

## <a name="powershell"></a>PowerShell
您可以使用 PowerShell 來管理資料庫和彈性資料庫集區，以及自動執行 Azure 資源部署。 若您要在生產環境中管理大量資料庫並自動化部署和資源變更，Microsoft 建議使用這項工具。

如需詳細資訊，請參閱[使用 PowerShell 管理 SQL Database](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>彈性資料庫工具
使用彈性資料庫工具執行動作，例如 

* 使用 [彈性工作](sql-database-elastic-jobs-overview.md)
* 使用 [分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)
* 使用 [Elastic Scale 用戶端程式庫](sql-database-elastic-database-client-library.md)，管理單一租用戶模型或多租用戶模型中的資料庫。

## <a name="additional-resources"></a>其他資源
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Azure 自動化](https://azure.microsoft.com/documentation/services/automation/)
* [Azure 排程器](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Nov16_HO3-->


