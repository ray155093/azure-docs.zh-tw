
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
為了節省成本，您可以隨選暫停和繼續計算資源。例如，如果您在夜間和週末不會使用資料庫，可以在這段時間暫停，並且在白天時繼續。資料庫暫停時，不會向您收取 DWU 的費用。

當您暫停資料庫時︰

* 計算和記憶體資源會傳回資料中心內的可用資源集區
* 暫停期間 DWU 的成本是零。
* 不會影響資料儲存體，您的資料保持不變。 
* SQL 資料倉儲會取消所有執行中或已排入佇列的作業。

<!---HONumber=AcomDC_0427_2016-->