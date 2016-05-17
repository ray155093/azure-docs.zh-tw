<properties 
    pageTitle="管理彈性資料庫集區 (PowerShell) | Microsoft Azure" 
    description="了解如何使用 PowerShell 管理彈性資料庫集區。"  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/28/2016"
    ms.author="sidneyh"/>

# 透過 PowerShell 監視和管理彈性資料庫集區 

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

使用 PowerShell Cmdlet 管理[彈性資料庫集區](sql-database-elastic-pool.md)。

如需常見的錯誤碼，請參閱 [SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題](sql-database-develop-error-messages.md)。

集區值可在 [eDTU 和儲存體限制](sql-database-elastic-pool#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)中找到。

## 必要條件
* Azure PowerShell 1.0 或更新版本。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。
* 只有 SQL Database V12 伺服器才可以使用彈性資料庫集區。如果您有 SQL Database V11 伺服器，可以在單一步驟中[使用 PowerShell 升級至 V12 並建立集區](sql-database-upgrade-server-portal.md)。


## 將資料庫移入彈性集區

您可以使用 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx)，將資料庫移入或移出集區。

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 變更集區的效能設定

當犧牲效能時，您可以變更集區的設定，以配合效能成長。使用 [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) Cmdlet。為每個集區的 eDTU 設定 -Dtu 參數。請參閱 [eDTU 和儲存體限制](sql-database-elastic-pool#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)以了解可能的值。

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## 取得集區作業的狀態

建立集區可能會很耗時。要追蹤集區作業的狀態，包括建立及更新作業，請使用 [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) Cmdlet。

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## 取得將彈性資料庫移入和移出集區的狀態

移動資料庫可能會很耗時。使用 [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) Cmdlet 追蹤移動狀態。

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 取得集區的資源使用量資料

可以用資源集區限制的百分比來擷取的度量：


| 度量名稱 | 說明 |
| :-- | :-- |
| cpu\_percent | 集區限制的平均計算使用量百分比。 |
| physical\_data\_read\_percent | 集區限制的平均 I/O 使用量百分比。 |
| log\_write\_percent | 集區限制的平均寫入資源使用量百分比。 | 
| DTU\_consumption\_percent | 集區 eDTU 限制的平均 eDTU 使用量百分比。 | 
| storage\_percent | 集區儲存體限制的平均儲存體使用量百分比。 |  
| workers\_percent | 集區限制的並行背景工作角色 (要求) 百分比。 |  
| sessions\_percent | 集區限制的並行工作階段百分比。 | 
| eDTU\_limit | 間隔期間此彈性集區目前最大的彈性集區 DTU 設定。 |
| storage\_limit | 間隔期間此彈性集區目前最大的彈性集區儲存體限制設定 (MB)。 |
| eDTU\_used | 此間隔期間集區使用的平均 eDTU。 |
| storage\_used | 此間隔期間集區使用的平均儲存體位元組數。 |

**度量資料粒度/保留期限：**

* 系統會以 5 分鐘的資料粒度傳回資料。  
* 資料會保留 14 天。  

此 Cmdlet 和 API 會將一次呼叫中可擷取的資料列限制為 1,000 個 (大約是 3 天份且資料粒度為 5 分鐘的資料)。但可以用不同的開始/結束時間間隔來多次呼叫此命令，以擷取更多資料。

要擷取度量：

	$metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## 取得彈性資料庫的資源使用量資料

除了以下的語意差異外，這些 API 與目前用於監視獨立資料庫之資源使用率的 (V12) API 相同。

這個擷取的 API 度量，會以針對該集區所設定之每個eDTU 上限 (或是 CPU、IO 等基礎度量的相等上限) 的百分比來表示。例如，這些度量有其中一項的使用率為 50%，則表示特定資源的消耗量佔該資源在父集區中，每個資料庫上限限制的 50%。

要擷取度量：

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## 範例︰擷取集區和其資料庫的資源消耗度量

此範例會擷取指定彈性集區和其所有資料庫的耗用量度量資訊。收集的資料會格式化並寫入.csv 格式檔案。可以使用 Excel 瀏覽檔案。

	$subscriptionId = '<Azure subscription id>'	      # Azure subscription ID
	$resourceGroupName = '<resource group name>'             # Resource Group
	$serverName = <server name>                              # server name
	$poolName = <elastic pool name>                          # pool name
		
	# Login to Azure account and select the subscription.
	Login-AzureRmAccount
	Set-AzureRmContext -SubscriptionId $subscriptionId
	
	# Get resource usage metrics for an elastic pool for the specified time interval.
	$startTime = '4/27/2016 00:00:00'  # start time in UTC
	$endTime = '4/27/2016 01:00:00'    # end time in UTC
	
	# Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
	$poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
	$poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
	
	# Get the list of databases in this pool.
	$dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
	
	# Get resource usage metrics for a database in an elastic database for the specified time interval.
	$dbMetrics = @()
	foreach ($db in $dbList)
	{
	    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
	    $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
	}
	
	#Optionally you can format the metrics and output as .csv file using the following script block.
	$command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
	}
	
	# Format and output pool metrics
	Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
	
	# Format and output database metrics
	Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## 彈性集區的作業延遲

- 每個資料庫的最小 eDTU 數或每個資料庫的最大 eDTU 數變更作業通常在 5 分鐘內即可完成。
- 集區的 eDTU 變更作業，需視集區中所有資料庫使用的總空間量而定。變更作業平均每 100 GB 會在 90 分鐘以內完成。舉例來說，如果集區中所有資料庫使用的總空間為 200 GB，則每集區 eDTU 變更作業的預期延遲時間會少於 3 小時。

## 從 V11 移轉到 V12 伺服器

PowerShell Cmdlet 可用來啟動、停止或監視從 V11 或任何其他 V12 以前版本升級至 Azure SQL Database V12。

- [使用 PowerShell 升級至 SQL Database V12](sql-database-upgrade-server-powershell.md)

如需這些 PowerShell Cmdlet 的參考文件，請參閱：


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Stop- Cmdlet 表示取消，不是暫停。升級一旦停止就沒有任何方法可以繼續，只能再次從頭開始。Stop- Cmdlet 會清除並釋放所有適當的資源。

## 後續步驟

- [建立彈性工作](sql-database-elastic-jobs-overview.md) 彈性工作可讓您對集區中任意數目的資料庫執行 T-SQL 指令碼。
- 請參閱[使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)︰使用彈性資料庫工具相應放大、移動資料、查詢或建立交易。

<!---HONumber=AcomDC_0504_2016-->