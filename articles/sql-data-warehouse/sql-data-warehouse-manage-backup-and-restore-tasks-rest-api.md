<properties
   pageTitle="Azure SQL 資料倉儲中的備份與還原 (REST) | Microsoft Azure"
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
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# 備份與還原 Azure SQL 資料倉儲中的資料庫 (REST)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [入口網站](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

如何使用 REST API 備份與還原 SQL 資料倉儲中的資料庫。

本主題中的工作：

- 還原即時資料庫
- 還原已刪除的資料庫
- 從不同的 Azure 地理區域還原無法存取的資料庫

[AZURE.INCLUDE [SQL 資料倉儲備份保留原則](../../includes/sql-data-warehouse-backup-retention-policy.md)]

## 開始之前

**請驗證您的 SQL Database DTU 容量。** 由於 SQL 資料倉儲會還原至位於您邏輯 SQL Server 上的新資料庫，請務必確保您還原至的 SQL Server 擁有足夠的 DTU 容量可供新資料庫使用。請參閱此部落格文章以深入了解[如何檢視和增加 DTU 配額][]。

## 還原即時資料庫

還原資料庫：

1. 使用 Get 資料庫還原點作業取得資料庫還原點清單。
2. 使用[建立資料庫還原要求][]作業來開始還原。
3. 使用[資料庫作業狀態][]作業來追蹤還原狀態。

>[AZURE.NOTE] 還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。

## 還原已刪除的資料庫

還原已刪除的資料庫

1.	使用[列出可還原的已卸除資料庫][]作業來列出所有可還原的已刪除資料庫。
2.	使用[取得可還原的已卸除資料庫][]作業來取得您想要還原之已刪除資料庫的詳細資料。
3.	使用[建立資料庫還原要求][]作業來開始還原。
4.	使用[資料庫作業狀態][]作業來追蹤還原狀態。

>[AZURE.NOTE] 還原完成後，您可以遵循[完成復原的資料庫][]指南來設定復原的資料庫。

## 從異地備援備份異地還原資料倉儲

執行異地還原：

1. 使用[列出可復原的資料庫][]作業來取得可復原的資料庫清單。
2. 使用[取得可復原的資料庫][]作業來取得您要復原的資料庫。
3. 使用[建立資料庫復原要求][]作業來建立復原要求。
4. 使用[資料庫作業狀態][]作業來追蹤復原狀態。

### 在執行異地還原後設定您的資料庫
這份檢查清單可以用來幫助您準備產生復原的資料庫。

1. **更新連接字串**：確認您用戶端工具的連接字串指向剛復原的資料庫。
2. **修改防火牆規則**：確認目標伺服器上的防火牆規則，並確定已啟用您用戶端電腦或 Azure 與伺服器以及剛復原之資料庫的連線。
3. **確認伺服器登入和資料庫使用者**：確認應用程式使用的所有登入是否都存在於主控您已復原之資料庫的伺服器上。重新建立缺少的登入，並將適當的已復原資料庫之權限授與登入。 
4. **啟用稽核**：如果需要稽核才能存取您的資料庫，則您必須在資料庫復原之後啟用稽核。

如果來源資料庫是啟用 TDE，則復原的資料庫將是啟用 TDE。


## 後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][]。

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

<!---HONumber=AcomDC_0518_2016-->