<properties
   pageTitle="Azure SQL 資料倉儲中的資料庫還原 (Azure 入口網站) | Microsoft Azure"
   description="還原 Azure SQL 資料倉儲中的即時、已刪除或無法存取之資料庫的 REST API 工作。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="elfish;barbkess;sonyama;kevin"/>

# 備份與還原 Azure SQL 資料倉儲中的資料庫 (REST API)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-manage-database-restore.md)
- [入口網站](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

在 SQL 資料倉儲中還原資料庫的 REST API 工作。

本主題中的工作：

- 還原即時資料庫
- 還原已刪除的資料庫

## 開始之前

**請驗證您的 SQL Database DTU 容量。** 由於 SQL 資料倉儲會還原至位於您邏輯 SQL Server 上的新資料庫，請務必確保您還原至的 SQL Server 擁有足夠的 DTU 容量可供新資料庫使用。如需有關[如何檢視和增加 DTU 配額][]的詳細資訊，請參閱此部落格文章。

## 還原即時資料庫

還原資料庫：

1. 使用 Get 資料庫還原點作業取得資料庫還原點清單。
2. 使用[建立資料庫還原要求][]作業來開始還原。
3. 使用[資料庫作業狀態][]作業來追蹤還原狀態。

>[AZURE.NOTE] 還原完成後，您可以依照[完成復原的資料庫][]指南來設定復原的資料庫。

## 還原已刪除的資料庫

還原已刪除的資料庫

1.	使用[列出可還原的已卸除資料庫][]作業來列出所有可還原的已刪除資料庫。
2.	使用[取得可還原的已卸除資料庫][]作業來取得您想要還原之已刪除資料庫的詳細資料。
3.	使用[建立資料庫還原要求][]作業來開始還原。
4.	使用[資料庫作業狀態][]作業來追蹤還原狀態。

>[AZURE.NOTE] 還原完成後，您可以依照[完成復原的資料庫][]指南來設定復原的資料庫。


## 後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請參閱 [Azure SQL Database 商務持續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: sql-database-business-continuity.md
[完成復原的資料庫]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[建立資料庫還原要求]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[資料庫作業狀態]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[取得可還原的已卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[列出可還原的已卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[如何檢視和增加 DTU 配額]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0608_2016-->