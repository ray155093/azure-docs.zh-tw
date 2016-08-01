<properties
   pageTitle="還原 Azure SQL 資料倉儲 (REST API) | Microsoft Azure"
   description="還原 Azure SQL 資料倉儲的 REST API 工作。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="sonyama;barbkess"/>

# 還原 Azure SQL 資料倉儲 (REST API)

> [AZURE.SELECTOR]
- [概觀][]
- [入口網站][]
- [PowerShell][]
- [REST][]

在本文中，您將了解如何使用 REST API 來還原 Azure SQL 資料倉儲。

## 開始之前

**請驗證您的 DTU 容量。** 每個 SQL 資料倉儲均由具有預設 DTU 配額的 SQL 伺服器裝載 (例如 myserver.database.windows.net)。在您還原 SQL 資料倉儲之前，請確認您的 SQL 伺服器有足夠的剩餘 DTU 配額供要還原的資料庫使用。若要了解如何計算所需 DTU 或要求更多 DTU，請參閱[要求 DTU 配額變更][]。

## 還原作用中或已暫停的資料庫

還原資料庫：

1. 使用 Get 資料庫還原點作業取得資料庫還原點清單。
2. 使用[建立資料庫還原要求][]作業來開始還原。
3. 使用[資料庫作業狀態][]作業來追蹤還原狀態。

>[AZURE.NOTE] 還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。

## 還原已刪除的資料庫

還原已刪除的資料庫：

1.	使用[列出可還原的已卸除資料庫][]作業來列出所有可還原的已刪除資料庫。
2.	使用[取得可還原的已卸除資料庫][]作業來取得您想要還原之已刪除資料庫的詳細資料。
3.	使用[建立資料庫還原要求][]作業來開始還原。
4.	使用[資料庫作業狀態][]作業來追蹤還原狀態。

>[AZURE.NOTE] 還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。


## 後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: ./sql-database-business-continuity.md
[要求 DTU 配額變更]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[完成復原的資料庫]: ./sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[建立資料庫還原要求]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[資料庫作業狀態]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[取得可還原的已卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[列出可還原的已卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0720_2016-->