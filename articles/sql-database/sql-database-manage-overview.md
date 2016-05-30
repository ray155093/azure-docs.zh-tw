<properties
	pageTitle="概觀：SQL Database 的管理工具"
	description="比較管理 Azure SQL Database 的工具和選項"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="sstein"/>

# 概觀：SQL Database 的管理工具

本主題將探索並比較管理 Azure SQL 資料庫的工具和選項，讓您可以挑選適合您工作、業務和您自身的正確工具。要選擇正確的工具就必須考量到您管理多少資料庫、工作本身以及執行工作的頻率。

## Azure 入口網站

[Azure 入口網站](https://portal.azure.com)是網頁式應用程式，您可以在其中建立、更新和刪除資料庫和邏輯伺服器，以及監視資料庫活動。如果您剛開始使用 Azure，或管理少量的資料庫，或是需要快速做某件事，那這正是十分適合您的工具。

如需關於使用入口網站的詳細深入資訊，請參閱[使用 Azure 傳統入口網站管理 SQL Database](sql-database-manage-portal.md)。

## Visual Studio 中的 SQL Server Management Studio 和 SQL Server Data Tools

Visual Studio 中的 SQL Server Management Studio (SSMS) 和 SQL Server Data Tools (SSDT) 都是可在您電腦上執行的用戶端工具，並可讓您連接、管理和開發雲端中的資料庫。如果您是熟悉 Visual Studio 或其他整合式開發環境 (IDE) 的應用程式開發人員，[請嘗試使用 Visual Studio 中的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)。許多資料庫系統管理員都熟悉可和 Azure SQL Database 搭配使用的 SSMS。[下載最新版的 SSMS](https://msdn.microsoft.com/library/mt238290)，並一律以最新版和 Azure SQL Database 搭配使用。如需使用 SSMS 管理 Azure SQL Database 的詳細資訊，請參閱[使用 SSMS 管理 SQL Database](sql-database-manage-azure-ssms.md)。

> [AZURE.IMPORTANT] 您必須一律使用最新版本的 SQL Server Management Studio 和 SQL Server Data Toools for Visual Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。舊版工具無法順利搭配 SQL Database 運作。

## 命令列工具。

您可以使用命令列工具 (例如 PowerShell) 來管理資料庫和彈性資料庫集區，以及自動化 Azure 資源部署工作。若您要在生產環境中管理大量資料庫並自動化部署和資源變更，Microsoft 建議使用這項工具。

如需使用命令列工具管理 Azure SQL Database 的詳細資訊，請參閱[使用 PowerShell 管理 SQL Database](sql-database-command-line-tools.md)

## 彈性資料庫工具
使用彈性資料庫工具執行動作，例如

* 使用[彈性工作](sql-database-elastic-jobs-overview.md)，針對一組資料庫執行 T-SQL 指令碼
* 使用[分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)將多租用戶模型資料庫移至單一租用戶模型
* 使用 [Elastic Scale 用戶端程式庫](sql-database-elastic-database-client-library.md)，管理單一租用戶模型或多租用戶模型中的資料庫。
 

<!---HONumber=AcomDC_0518_2016-->