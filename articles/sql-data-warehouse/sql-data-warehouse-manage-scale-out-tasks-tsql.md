<properties
pageTitle="Azure SQL 資料倉儲 (TSQL) 的管理延展性工作 | Microsoft Azure"
   description="針對 Azure SQL 資料倉儲相應放大效能的 TSQL 工作。調整 DWU 以使用 TSQL 來變更計算資源。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL 資料倉儲 (TSQL) 的管理延展性工作

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-scalability.md)
- [入口網站](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


彈性相應放大計算資源和記憶體，以滿足不斷變動的工作負載，並在非尖峰時間相應縮減資源以節省成本。

這個工作集合會使用 TSQL 來：

- 檢視目前的 DWU 設定
- 調整 DWU 以變更計算資源

若要暫停或繼續資料庫，請在這篇文章頂端選擇其中一個其他平台選項。

若要了解這個問題，請參閱[效能延展性概觀][]。

<a name="current-dwu-bk"></a>

## 檢視目前的 DWU 設定

若要檢視您的資料庫的目前 DWU 設定︰

1. 在 Visual Studio 2015 中開啟 [SQL Server 物件總管]。
2. 連接到與邏輯 SQL Database 伺服器相關聯的 master 資料庫。
2. 從 sys.database\_service\_objectives 動態管理檢視中選取。下列是一個範例： 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>

## 調整 DWU

[AZURE.INCLUDE [SQL 資料倉儲調整 DWU 描述](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWU︰


1. 連接到與您的邏輯 SQL Database 伺服器相關聯的 master 資料庫。
2. 使用 [ALTER DATABASE][] TSQL 陳述式。下例範例會將資料庫 MySQLDW 的服務等級目標設定為 DW1000。 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## 後續步驟

如需其他管理工作的詳細資訊，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[效能延展性概觀]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->