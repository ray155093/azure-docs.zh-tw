<properties 
   pageTitle="Log Analytics 中的 Windows 和 Linux 的效能計數器 |Microsoft Azure"
   description="Log Analytics 會收集效能計數器以分析 Windows 和 Linux 代理程式的效能。  本文說明如何設定 Windows 和 Linux 代理程式的效能計數器收集、儲存在 OMS 儲存機制中的相關詳細資料，以及如何在 OMS 入口網站中分析這些資料。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />


# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Log Analytics 中的 Windows 和 Linux 效能資料來源 

Windows 和 Linux 的效能計數器可讓您深入了解硬體元件、作業系統及應用程式的效能。  Log Analytics 可收集效能計數器，以頻繁間隔進行接近即時 (NRT) 的分析，並彙總較長期分析和報告所需的效能資料。

![效能計數器](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>設定效能計數器

從 [Log Analytics [設定] 中的 [資料] 功能表](log-analytics-data-sources.md#configuring-data-sources)設定效能計數器。

當您第一次為新的 OMS 工作區設定 Windows 或 Linux 效能計數器時，系統會提供選項，讓您快速建立數個常用的計數器。  這些計數器旁邊皆會列出核取方塊。  確認已核取所有想一開始就建立的計數器，然後按一下 [Add the selected performance counters] (加入選取的效能計數器) 。

![設定 Windows 效能計數器](media/log-analytics-data-sources-performance-counters/configure-windows.png)

請遵循此程序以加入要收集的新 Windows 效能計數器。

1. 在文字方塊中輸入計數器名稱，格式為 *object(instance)\counter*。  開始輸入時，您就會看到符合的常用計數器清單。  您可以從清單中選取計數器，或自行輸入。  您也可以指定 *object\counter*，以傳回特定計數器的所有執行個體。 
2. 按一下 **+** 或按 **Enter**，將計數器新增至清單。
3. 新增計數器時，它會以 10 秒作為 [取樣間隔時間] 的預設值。  如果您想要降低所收集之效能資料的儲存需求，可以將此值變更為最多 1800 秒 (30 分鐘)。
4. 加入所要的計數器後，請按一下畫面頂端的 [儲存]  按鈕以儲存設定。

![設定 Linux 效能計數器](media/log-analytics-data-sources-performance-counters/configure-linux.png)

請遵循此程序以加入要收集的新 Linux 效能計數器。

1. 根據預設，所有組態變更都會自動發送給所有代理程式。  若是 Linux 代理程式，組態檔會傳送給 Fluentd 資料收集器。  如果您想以手動方式在每個 Linux 代理程式上修改這個檔案，只要取消核取 [Apply below configuration to my Linux machines] (將下列組態套用至我的 Linux 機器) 方塊即可。
2. 在文字方塊中輸入計數器名稱，格式為 *object(instance)\counter*。  開始輸入時，您就會看到符合的常用計數器清單。  您可以從清單中選取計數器，或自行輸入。  
2. 按一下 **+** 或按 **Enter**，將計數器新增至物件的其他計數器清單。
3. 物件的所有計數器都會使用相同的 [取樣間隔時間] 。  預設值為 10 秒。  如果您想要降低所收集之效能資料的儲存空間需求，請將此值變更為最多 1800 秒 (30 分鐘)。
4. 加入所要的計數器後，請按一下畫面頂端的 [儲存]  按鈕以儲存設定。

## <a name="data-collection"></a>資料收集

只要代理程式有安裝相關計數器，Log Analytics 就會依照其指定的取樣間隔時間，收集全部代理程式上所有指定的效能計數器。  資料不彙總，在 OMS 訂用帳戶的指定期間，所有記錄搜尋檢視中都會提供未經處理資料。


## <a name="performance-record-properties"></a>效能記錄屬性

效能記錄都具有 **Perf** 類型以及下表中的屬性。

| 屬性 | 說明 |
|:--|:--|
| 電腦         | 收集事件的來源電腦。 |
| CounterName      | 效能計數器的名稱 |
| CounterPath      | 計數器的完整路徑，格式為 \\\\\<電腦>\\object(instance)\\counter。 |
| CounterValue     | 計數器的數值。  |
| InstanceName     | 事件執行個體的名稱。  如果沒有執行個體即為空白。 |
| ObjectName       | 效能物件的名稱 |
| SourceSystem  | 收集資料的來源代理程式類型。 <br> OpsManager – Windows 代理程式，直接連接或 SCOM <br>  Linux – 所有的 Linux 代理程式  <br>  AzureStorage – Azure 診斷 |
| TimeGenerated       | 資料取樣的日期和時間。 |


## <a name="sizing-estimates"></a>大小估計值

 特定計數器集合的約略估計是依照每個執行個體 10 秒間隔，每天約 1 MB。  您可以使用下列公式，評估特定計數器的儲存需求。

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>記錄搜尋與效能記錄

下表提供擷取效能記錄的不同記錄搜尋範例。

| 查詢 | 說明 |
|:--|:--|
| Type=Perf | 所有效能資料 |
| Type=Perf Computer="MyComputer" | 來自特定電腦的所有效能資料 |
| Type=Perf CounterName="Current Disk Queue Length" | 來自特定計數器的所有效能資料 |
| Type=Perf (ObjectName=Processor) CounterName="% Processor Time" InstanceName=_Total &#124; measure Avg(Average) as AVGCPU  by Computer | 所有電腦的平均 CPU 使用率 |
| Type=Perf (CounterName="% Processor Time") &#124;  measure max(Max) by Computer | 所有電腦的最大 CPU 使用率 |
| Type=Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" &#124; measure Avg(Average) by InstanceName | 指定電腦之所有執行個體的平均目前磁碟佇列長度 |
| Type=Perf CounterName="DiskTransfers/sec" &#124; measure percentile95(Average) by Computer | 所有電腦之第 95 個百分位數的 Disk Transfers/Sec |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  &#124; measure avg(CounterValue) by Computer Interval 1HOUR | 所有電腦每小時平均 CPU 使用率 |
| Type=Perf Computer="MyComputer" CounterName=%* InstanceName=_Total &#124; measure percentile70(CounterValue) by CounterName Interval 1HOUR | 特定電腦每小時每個 % 百分比計數器的 70 個百分位數 |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  (Computer="MyComputer") &#124; measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR | 特定電腦每小時平均、最小、最大和 75 個百分位數的 CPU 使用量 |

## <a name="viewing-performance-data"></a>檢視效能資料

在執行效能資料的記錄搜尋時，預設會顯示 [記錄] 檢視。  若要以圖形形式檢視資料，請按一下 [計量]。  如需詳細的圖形檢視，請按一下計數器旁的 **+**。  

![摺疊的計量檢視](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

如果您選取的時間範圍在 6 小時以內，則圖形每隔幾秒更新一次。  圖形的右邊會以淺藍色顯示即時資料。

![展開計量檢視與即時資料](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

若要彙總記錄搜尋中的效能資料，請參閱[在 OMS 中隨需彙總和視覺化度量](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/)。

## <a name="next-steps"></a>後續步驟

- 了解 [記錄搜尋](log-analytics-log-searches.md) ，其可分析從資料來源和方案所收集的資料。  
- 將收集的資料匯出至 [Power BI](log-analytics-powerbi.md) 以進行其他視覺效果和分析。


<!--HONumber=Oct16_HO2-->


