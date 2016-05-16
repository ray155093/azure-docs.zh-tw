<properties
   pageTitle="Azure SQL 資料倉儲 (REST) 的管理延展性工作 | Microsoft Azure"
   description="針對 Azure SQL 資料倉儲相應放大效能的 REST API 工作。調整 DWU 以變更計算資源。或者，暫停和繼續計算資源以節省成本。"
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

# Azure SQL 資料倉儲 (REST) 的管理延展性工作

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-overview-scalability.md)
- [入口網站](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

彈性相應放大計算資源和記憶體，以滿足不斷變動的工作負載，並在非尖峰時間相應縮減資源以節省成本。

這個工作集合會使用 REST API 來：

- 藉由調整 DWU 來調整效能
- 暫停計算資源
- 繼續計算資源

若要了解這個問題，請參閱[效能延展性概觀][]。

<a name="scale-performance-bk"></a>

## 調整效能

[AZURE.INCLUDE [SQL 資料倉儲調整 DWU 描述](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWU，請使用[建立或更新資料庫][] REST API。下例會將裝載在 MyServer 伺服器上的資料庫 MySQLDW 的服務等級目標設定為 DW1000。此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## 暫停計算

[AZURE.INCLUDE [SQL 資料倉儲暫停描述](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，請使用[暫停資料庫][] REST API。下例會暫停裝載在 Server01 伺服器上的 Database02 資料庫。此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## 繼續計算

[AZURE.INCLUDE [SQL 資料倉儲繼續描述](../../includes/sql-data-warehouse-resume-description.md)]

若要啟動資料庫，請使用[繼續資料庫][] REST API。下例會啟動裝載在 Server01 伺服器上的 Database02 資料庫。此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## 後續步驟

如需其他管理工作的詳細資訊，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[效能延展性概觀]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[暫停資料庫]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[繼續資料庫]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[建立或更新資料庫]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->