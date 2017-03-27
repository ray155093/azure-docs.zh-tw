---
title: "PowerShell：建立及管理 Azure SQL 彈性集區 | Microsoft Docs"
description: "了解如何使用 PowerShell 管理彈性集區。"
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 61289770-69b9-4ae3-9252-d0e94d709331
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 06/22/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ffcf0f0aa80f0a6b65cbef65e361e4830fcca3ff
ms.lasthandoff: 03/10/2017


---

# <a name="create-and-manage-an-elastic-pool-with-powershell"></a>使用 PowerShell 建立及管理彈性集區
本主題說明如何使用 PowerShell 建立及管理可調整的[彈性集區](sql-database-elastic-pool.md)。  您也可以使用 [Azure 入口網站](https://portal.azure.com/)、REST API 或 [C#](sql-database-elastic-pool-manage-csharp.md) 來建立及管理 Azure 彈性集區。 您也可以使用 [Transact-SQL](sql-database-elastic-pool-manage-tsql.md) 來建立資料庫並將它移入和移出彈性集區。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-elastic-pool"></a>建立彈性集區
[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) Cmdlet 會建立彈性集區。 每個集區的 eDTU 值、最小和最大 DTU 會受限於服務層值 (基本、標準或進階)。 請參閱[彈性集區和集區資料庫的 eDTU 和儲存體限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)。

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>在彈性集區中建立集區資料庫
使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) Cmdlet，並將 **ElasticPoolName** 參數設定為目標集區。 若要將現有的資料庫移入彈性集區，請參閱[將資料庫移入彈性集區](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool)。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### <a name="complete-script"></a>完整的指令碼
此指令碼會建立 Azure 資源群組和伺服器。 出現提示時，提供適用於新伺服器的系統管理員使用者名稱和密碼 (而非您 Azure 認證)。

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB

## <a name="create-an-elastic-pool-and-add-multiple-pooled-databases"></a>建立彈性集區並新增多個集區資料庫
使用入口網站或一次只建立單一資料庫的 PowerShell Cmdlet 在彈性集區中建立許多資料庫可能需要花費一些時間。 若要自動建立成彈性集區，請參閱 [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。   

## <a name="move-a-database-into-an-elastic-pool"></a>將資料庫移入彈性集區
您可以使用 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) 將資料庫移入或移出彈性集區。

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-an-elastic-pool"></a>變更彈性集區的效能設定
當犧牲效能時，您可以變更集區的設定，以配合效能成長。 使用 [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511\(v=azure.300\).aspx) Cmdlet。 為每個集區的 eDTU 設定 -Dtu 參數。 請參閱 [eDTU 和儲存體限制](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)以了解有哪些可能的值。  

    Set-AzureRmSqlElasticPool -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1” -Dtu 1200 -DatabaseDtuMax 100 -DatabaseDtuMin 50

## <a name="get-the-status-of-pool-operations"></a>取得集區作業的狀態
建立彈性集區可能會很耗時。 要追蹤集區作業的狀態，包括建立及更新作業，請使用 [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812\(v=azure.300\).aspx) Cmdlet。

    Get-AzureRmSqlElasticPoolActivity -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1”

## <a name="get-the-status-of-moving-a-database-into-and-out-of-an-elastic-pool"></a>取得將資料庫移入和移出彈性集區的狀態
移動資料庫可能會很耗時。 使用 [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687\(v=azure.300\).aspx) Cmdlet 追蹤移動狀態。

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>取得彈性集區的資源使用量資料
可以用資源集區限制的百分比來擷取的度量：   

| 度量名稱 | 說明 |
|:--- |:--- |
| cpu\_percent |集區限制的平均計算使用量百分比。 |
| 實體\_資料\_讀取\_百分比 |集區限制的平均 I/O 使用量百分比。 |
| 記錄檔\_撰寫\_百分比 |集區限制的平均寫入資源使用量百分比。 |
| DTU\_耗用量\_百分比 |集區 eDTU 限制的平均 eDTU 使用量百分比。 |
| 儲存體\_percent |集區儲存體限制的平均儲存體使用量百分比。 |
| 背景工作角色\_百分比 |集區限制的並行背景工作角色 (要求) 百分比。 |
| 工作階段\_百分比 |集區限制的並行工作階段百分比。 |
| eDTU_limit |間隔期間此彈性集區目前最大的彈性集區 DTU 設定。 |
| 儲存體\_限制 |間隔期間此彈性集區目前最大的彈性集區儲存體限制設定 (MB)。 |
| eDTU\_使用 |此間隔期間集區使用的平均 eDTU。 |
| 儲存體\_使用 |此間隔期間集區使用的平均儲存體位元組數。 |

**度量資料粒度/保留期限：**

* 系統會以 5 分鐘的資料粒度傳回資料。  
* 資料會保留 35 天。  

此 Cmdlet 和 API 會將一次呼叫中可擷取的資料列限制為 1000 個 (大約是 3 天份且資料粒度為 5 分鐘的資料)。 但可以用不同的開始/結束時間間隔來多次呼叫此命令，以擷取更多資料。

擷取度量：

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  

## <a name="get-resource-usage-data-for-a-database-in-an-elastic-pool"></a>取得彈性集區中資料庫的資源使用量資料
這些 API 與目前用來監視單一資料庫之資源使用率的 (V12) API 相同，唯一的差別在於下列語意：擷取的度量是以為該集區設定的每個資料庫最大 eDTU 百分比形式表示 (或相等於 CPU 或 IO 等基礎度量的上限)。 例如，這些度量有其中一項的使用率為 50%，則表示特定資源的消耗量佔該資源在父集區中，每個資料庫上限限制的 50%。

擷取度量：

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

## <a name="add-an-alert-to-an-elastic-pool-resource"></a>將警示新增到彈性集區資源
您可以將警示規則新增到彈性集區中，以在彈性集區達到您設定的使用率閾值時，將電子郵件通知或警示字串傳送到 [URL 端點](https://msdn.microsoft.com/library/mt718036.aspx)。 使用 Add-AzureRmMetricAlertRule Cmdlet。

> [!IMPORTANT]
> 監視彈性集區的資源使用率至少有 20 分鐘的延遲。 目前不支援將彈性集區的警示設定為小於 30 分鐘。 任何針對彈性集區所設定且期間 (PowerShell API 中名為 “-WindowSize” 的參數) 小於 30 分鐘的警示可能都不會觸發。 請確定您針對彈性集區定義的任何警示都會使用 30 分鐘以上的期間 (WindowSize)。
>
>

此範例會新增一個可在彈性集區 eDTU 耗用量超過特定閾值時接獲通知的警示。

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

## <a name="add-alerts-to-all-databases-in-an-elastic-pool"></a>將警示新增到彈性集區中的所有資料庫
您可以將警示規則新增到彈性集區中的所有資料庫，以在資源達到警示所設定的使用率臨界值時，將電子郵件通知或警示字串傳送到 [URL 端點](https://msdn.microsoft.com/library/mt718036.aspx) 。

> [!IMPORTANT]
> 監視彈性集區的資源使用率至少有 20 分鐘的延遲。 目前不支援將彈性集區的警示設定為小於 30 分鐘。 任何針對彈性集區所設定且期間 (PowerShell API 中名為 “-WindowSize” 的參數) 小於 30 分鐘的警示可能都不會觸發。 請確定您針對彈性集區定義的任何警示都會使用 30 分鐘以上的期間 (WindowSize)。
>
>

此範例會將警示新增到彈性集區中的每個資料庫，以在資料庫的 DTU 耗用量超過特定閾值時接獲通知。

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    }

## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>收集和監視訂用帳戶中多個集區的資源使用量資料
當訂用帳戶中有許多資料庫時，難以分開監視每個彈性集區。 因此，可以結合 SQL Database PowerShell Cmdlet 和 T-SQL 查詢，從多個集區與其資料庫收集資源使用量資料，以便監視和分析資源使用量。 在 GitHub 上的 SQL Server 範例存放庫中，可以找到這樣一組 PowerShell 指令碼的 [範例實作](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) ，以及其作用和使用方式的相關文件。

若要使用此範例實作，請依照下列步驟執行。

1. 下載[指令碼和文件](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)：
2. 為您的環境修改指令碼。 指定裝載彈性集區的一或多部伺服器。
3. 指定儲存所收集度量的遙測資料庫。
4. 自訂指令碼，以指定指令碼的執行持續時間。

概括而言，指令碼會執行下列作業︰

* 列舉指定的 Azure 訂用帳戶 (或指定的伺服器清單) 中的所有伺服器。
* 執行每部伺服器的背景作業。 作業會在迴圈中定期執行，並收集伺服器中所有集區的遙測資料。 然後將所收集的資料載入到指定的遙測資料庫。
* 列舉每個集區中的資料庫清單，以收集資料庫資源使用量資料。 然後將所收集的資料載入到遙測資料庫。

您可以分析遙測資料庫中收集的度量，以監視彈性集區和資料庫的健康狀態。 指令碼也會在遙測資料庫中安裝預先定義的資料表值函式 (TVF)，協助彙總指定時間範圍的度量。 例如，TVF 的結果可用來顯示「在指定的時間範圍內具有最大 eDTU 使用率的前 N 個彈性集區」。 或者，使用分析工具 (例如 Excel 或 Power BI) 來查詢和分析所收集的資料。

### <a name="example-retrieve-resource-consumption-metrics-for-an-elastic-pool-and-its-databases"></a>範例︰擷取彈性集區和其資料庫的資源消耗度量
此範例會擷取指定彈性集區和其所有資料庫的耗用量度量資訊。 收集的資料會格式化並寫入.csv 格式檔案。 可以使用 Excel 瀏覽檔案。

    $subscriptionId = '<Azure subscription id>'          # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name

    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC

    # Construct the pool resource ID and retrive pool metrics at 5-minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Get resource usage metrics for a database in an elastic pool for the specified time interval.
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

## <a name="latency-of-elastic-pool-operations"></a>彈性集區的作業延遲
* 每個資料庫的最小 eDTU 數或每個資料庫的最大 eDTU 數變更作業通常在 5 分鐘內即可完成。
* 集區的 eDTU 變更作業，需視集區中所有資料庫使用的總空間量而定。 變更作業平均每 100 GB 會在 90 分鐘以內完成。 舉例來說，如果集區中所有資料庫使用的總空間為 200 GB，則每集區 eDTU 變更作業的預期延遲時間會少於 3 小時。

如需這些 PowerShell Cmdlet 的參考文件，請參閱：

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582\(v=azure.300\).aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403\(v=azure.300\).aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589\(v=azure.300\).aspx)

Stop- Cmdlet 表示取消，不是暫停。 升級一旦停止就沒有任何方法可以繼續，只能再次從頭開始。 Stop- Cmdlet 會清除並釋放所有適當的資源。

## <a name="next-steps"></a>後續步驟
* [建立彈性工作](sql-database-elastic-jobs-overview.md) ：彈性工作可讓您對集區中任意數目的資料庫執行 T-SQL 指令碼。
* 請參閱[使用 Azure SQL Database 相應放大](sql-database-elastic-scale-introduction.md)︰使用彈性工具相應放大、移動資料、查詢或建立交易。

