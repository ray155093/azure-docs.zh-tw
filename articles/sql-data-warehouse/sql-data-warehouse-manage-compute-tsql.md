<properties
   pageTitle="管理 Azure SQL 資料倉儲中的計算能力 (REST) | Microsoft Azure"
   description="透過調整 DWU 以相應放大效能的 Transact-SQL (T-SQL) 工作。透過在非尖峰時間進行縮減以節省成本。"
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
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# 管理 Azure SQL 資料倉儲中的計算能力 (T-SQL)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-manage-compute-overview.md)
- [入口網站](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


透過相應放大計算資源和記憶體來調整效能，以滿足工作負載不斷改變的需求。透過在非尖峰時間縮減資源或直接暫停計算以節省成本。

這個工作集合會使用 T-SQL 來：

- 檢視目前的 DWU 設定
- 調整 DWU 以變更計算資源

若要暫停或繼續資料庫，請在這篇文章頂端選擇其中一個其他平台選項。

若要深入了解，請參閱[管理計算能力概觀][]。

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

<a name="scale-dwu-bk"></a> <a name="scale-compute-bk"></a>

## 調整計算

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
[管理計算能力概觀]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->