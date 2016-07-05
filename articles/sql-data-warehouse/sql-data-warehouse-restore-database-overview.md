<properties
   pageTitle="還原 Azure SQL 資料倉儲 (概觀) | Microsoft Azure"
   description="復原 Azure SQL 資料倉儲中資料庫之資料庫還原選項的概觀。"
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
   ms.date="06/15/2016"
   ms.author="elfish;barbkess;sonyama"/>


# 還原 Azure SQL 資料倉儲 (概觀)

> [AZURE.SELECTOR]
- [概觀][]
- [入口網站][]
- [PowerShell][]
- [REST][] Azure SQL 資料倉儲可使用本地備援儲存體和自動備份，保護您的資料。自動備份提供免管理的方式，以避免資料庫遭到意外損毀或刪除。如果使用者不小心或偶然修改或刪除資料，您可以將資料庫還原到較早的時間點，以確保商務持續性。SQL 資料倉儲使用 Azure 儲存體快照無縫地備份您的資料庫，而不需要任何停機時間。

## 自動備份

**作用中**資料庫至少每隔 8 小時就會自動備份一次並保留 7 天。這可讓您將作用中資料庫還原至過去 7 天內數個還原點的其中一個。

若資料庫已暫停，則新的備份將會停止，而先前的備份會在存留達到 7 天時卸除。如果資料庫已暫停超過 7 天，將會儲存最新的備份，確保您一律至少會有一個備份。

卸除資料庫時，最後一次備份會儲存 7 天。

在您作用中的 SQL 資料倉儲上執行此查詢，以查看取得最後一次備份的時間：

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

如果您需要將備份保留超過 7 天，您只要將其中一個還原點還原到新的資料庫，然後選擇性地暫停該資料庫，所以您僅需支付該備份的儲存空間。

## 資料備援

除了備份，SQL 資料倉儲也會利用[本地備援 (LRS)][] Azure 進階儲存體來保護您的資料。並在當地的資料中心維護多份資料的同步複本，確保當地語系化失敗時能夠提供透明的資料保護。資料備援可確保您的資料在基礎結構問題 (例如磁碟故障等) 中存留。資料備援可利用容錯和高可用性基礎結構來確保商務持續性。

## 還原資料庫

還原 SQL 資料倉儲是可以在 Azure 入口網站中完成簡單的作業，也可以使用 PowerShell 或 REST API 自動執行。


## 後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: ./sql-database-business-continuity.md
[本地備援 (LRS)]: ../storage/storage-redundancy.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0622_2016-->