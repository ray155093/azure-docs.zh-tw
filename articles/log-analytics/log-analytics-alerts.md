---
title: Log Analytics 中的警示 | Microsoft Docs
description: Log Analytics 中的警示會識別您的 OMS 儲存機制中的重要資訊，並可主動通知您相關問題或叫用動作以嘗試更正問題。  本文描述如何建立警示規則及詳細說明可以採取的各種動作。
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2016
ms.author: bwren

---
# <a name="alerts-in-log-analytics"></a>Log Analytics 中的警示
Log Analytics 中的警示會識別您的 OMS 儲存機制中的重要資訊。  警示規則會依據排程自動執行記錄檔搜尋，並且在結果符合特定準則時建立警示記錄。  此規則接著可以自動執行一或多個動作，以主動通知警示或叫用另一個處理序。   

![Log Analytics 警示](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>建立警示規則
若要建立警示規則，首先針對應叫用警示的記錄，建立記錄檔搜尋。  [警示]  按鈕將可供您使用，以便建立和設定警示規則。

1. 從 [OMS 概觀] 頁面，按一下 [記錄檔搜尋] 。
2. 建立新的記錄檔搜尋查詢，或選取已儲存的記錄檔搜尋。 
3. 按一下頁面頂端的 [警示] 以開啟 [新增警示規則] 畫面。
4. 請參閱下列表格，以取得設定警示的選項詳細資訊。
5. 當您提供警示規則的時間範圍時，將會顯示該時間範圍內符合搜尋準則的現有記錄數目。  這可以協助您判斷可為您提供預期結果數目的頻率。
6. 按一下 [儲存]  完成警示規則。  該警示規則會立即開始執行。

![新增警示規則](media/log-analytics-alerts/add-alert-rule.png)

| 屬性 | 說明 |
|:--- |:--- |
| **警示資訊** | |
| 名稱 |用以識別警示規則的唯一名稱。 |
| 嚴重性 |此規則所建立之警示的嚴重性。 |
| 搜尋查詢 |選取 [使用目前搜尋查詢]  以使用目前的查詢，或從清單中選取現有的已儲存搜尋。  查詢語法會提供於文字方塊中，您可以視需要加以修改。 |
| 時間範圍 |指定查詢的時間範圍。  查詢只會傳回在此目前時間範圍內建立的記錄。  可以是介於 5 分鐘與 24 小時之間的任何值。  應大於或等於警示頻率。  <br><br>  例如，如果時間範圍設定為 60 分鐘，則查詢會在下午 1:15 執行，只會傳回在下午 12:15 與下午 1:15 之間建立的記錄。 |
| **排程** | |
| 閾值 |何時建立警示的準則。  如果查詢傳回的記錄數目符合此準則，則會建立警示。 |
| 警示頻率 |指定應執行查詢的頻率。  可以是介於 5 分鐘與 24 小時之間的任何值。  應等於或小於此時間範圍。 |
| 隱藏警示 |當您開啟警示規則的隱藏功能時，此規則的動作會在建立新警示後停用並持續一段您所定義的時間。  採規則仍在執行中，並且會在符合準則時建立警示記錄。  這可讓您有時間更正問題，而不需執行重複的動作。 |
| **動作** | |
| 電子郵件通知 |如果您要在警示觸發時傳送電子郵件，請指定 [是]  。 |
| 主旨 |電子郵件的主旨。  您無法修改郵件的內文。 |
| 收件者 |所有電子郵件收件者的地址。  如果您指定多個地址，則使用分號 (;) 分隔這些地址。 |
| Webhook |如果您要在警示觸發時呼叫 Webhook，請指定 [是]  。 |
| Webhook URL |Webhook 的 URL。 |
| 包含自訂 JSON 承載 |如果您要使用自訂承載取代預設承載，請選取此選項。 |
| 輸入您的自訂 JSON 承載 |Webhook 的自訂承載。  如需詳細資料，請參閱上一節。 |
| Runbook |如果您要在警示觸發時啟動 Azure 自動化，請指定 [是]  。 |
| 啟動 Runbook |從自動化解決方案中設定的自動化帳戶中的 Runbook，選取要啟動的 Runbook。 |
| 執行位置 |選取 [Azure]  以在 Azure 雲端中執行 Runbook。  選取 [Hybrid Worker]  以在本機環境中的 [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) 上執行 Runbook。 |

## <a name="manage-alert-rules"></a>管理警示規則
您可以在 Log Analytics [設定] 的 [警示] 功能表中，取得所有警示規則的清單。  

![管理警示](./media/log-analytics-alerts/configure.png)

1. 在 OMS 主控台中選取 [設定]  圖格。
2. 選取 [警示] 。

您可以在此檢視中執行多個動作。

* 選取旁邊的 [關閉]  來停用規則。
* 按一下旁邊的鉛筆圖示以編輯警示規則。
* 按一下旁邊的 **X** 圖示以移除警示規則。 

## <a name="setting-time-windows"></a>設定時間範圍
### <a name="event-alerts"></a>事件警示
事件包括資料來源，例如 Windows 事件記錄檔、Syslog 和自訂記錄檔。  您可能希望在建立特定的錯誤事件，或是在特定的時間範圍內建立多個錯誤事件時建立警示。

若要對單一事件發出警示，請將結果數目設為大於 0 並將頻率與時間範圍設為 5 分鐘。  如此一來，將會每隔 5 分鐘執行一次查詢，並檢查在上次執行查詢後是否發生所建立的單一事件。  較長的頻率可能會延遲收集事件和建立警示的時間。

有些應用程式可能會記錄不一定會產生警示的偶發錯誤。  例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。  在此情況下，除非在特定時間範圍內建立多個事件，否則您不會想建立警示。  

在某些情況下，您可能想在某個事件不存在時建立警示。  例如，處理序可能會記錄一般事件，表示運作正常。  如果它不在特定的時間範圍內記錄一個事件，則應建立警示。  在此情況下，您會將臨界值設定為「小於 1」 。

### <a name="performance-alerts"></a>效能警示
[效能資料](log-analytics-data-sources-performance-counters.md) 會儲存為 OMS 儲存機制中類似事件的記錄。  每筆記錄的值即為在過去 30 分鐘測量到的平均值。  如果您要在效能計數器超過特定臨界值時發出警示，則該臨界值應包含在查詢中。

例如，如果您要在處理器執行超過 90% 的時間長達 30 分鐘時發出警示，您會使用類似 *Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90* 的查詢，而警示規則的臨界值會設為「大於 0」。  

 不論您收集每個計數器的頻率為何，每隔 30 分鐘都會彙總一次 [效能記錄](log-analytics-data-sources-performance-counters.md) ，所以小於 30 分鐘的時間範圍可能不會傳回任何記錄。  將時間範圍設定為 30 分鐘，可確保您對每個連接的來源取得單一記錄，以代表該段時間的平均值。

## <a name="alert-actions"></a>警示動作
除了建立警示記錄，您可以設定警示規則，以自動執行一或多個動作。  動作可以主動通知警示，或叫用某個處理序序，以嘗試更正偵測到的問題。  下列各節說明目前可用的動作。

### <a name="email-actions"></a>電子郵件動作
電子郵件動作會傳送內含警示詳細資料的電子郵件給一或多位收件者。  您可以指定郵件的主旨，但其內容是由 Log Analytics 所編製的標準格式。  除了記錄檔搜尋所傳回的最多 10 筆記錄的詳細資料以外，其中還包括摘要資訊 (如警示名稱)。  此外，也包括 Log Analytics 中將從該查詢傳回整個記錄集的記錄檔搜尋的連結。   此郵件的寄件者為 *Microsoft Operations Management Suite Team &lt;noreply@oms.microsoft.com&gt;*。 

### <a name="webhook-actions"></a>Webhook 動作
Webhook 動作可讓您透過單一 HTTP POST 要求叫用外部處理序。  被呼叫的服務應支援 Webhook，並判斷將如何使用所接收的承載。  只要此要求是 API 所認得的格式，您也可以呼叫不會特別支援 Webhook 的 REST API。  使用 Webhook 來回應警示的範例會使用 [Slack](http://slack.com) 等服務來傳送包含警示詳細資料的訊息，或在 [PagerDuty](http://pagerduty.com/) 等服務中建立事件。  

如需使用 Webhook 來呼叫範例服務以建立警示規則的完整逐步解說，請參閱 [Log Analytics 警示中的 Webhook](log-analytics-alerts-webhooks.md)。

Webhook 包括 URL 以及 JSON 格式的承載 (也就是傳送至外部服務的資料)。  根據預設，承載會包含下表中的值。  您可以選擇用自己的自訂承載來取代此承載。  在此情況下，您可以使用下表中每個參數的變數，將其值包含在您的自訂承載中。

| 參數 | 變數 | 說明 |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |警示規則的名稱。 |
| AlertThresholdOperator |#thresholdoperator |警示規則的臨界值運算子。  大於或等於。 |
| AlertThresholdValue |#thresholdvalue |警示規則的臨界值。 |
| LinkToSearchResults |#LinkToSearchResults |連結至 Log Analytics 記錄檔搜尋，而該搜尋會從建立警示的查詢傳回記錄。 |
| ResultCount |#searchresultcount |搜尋結果中的記錄數目。 |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |查詢的結束時間 (UTC 格式)。 |
| SearchIntervalInSeconds |#searchinterval |警示規則的時間範圍。 |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |查詢的開始時間 (UTC 格式)。 |
| SearchQuery |#SearchQuery |警示規則所使用的記錄檔搜尋查詢。 |
| SearchResults |請參閱下方 |查詢所傳回的記錄 (JSON 格式)。  受限於前 5,000 筆記錄。 |
| WorkspaceID |#WorkspaceID |OMS 工作區的識別碼。 |

例如，您可以指定下列自訂承載，其中包含稱為 text 的單一參數。  此 Webhook 所呼叫的服務需要有這個參數。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

此範例承載會在傳送到 Webhook 時解析如下。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

若要在自訂承載中包含搜尋結果，請將下面一行新增為 json 承載中的最上層屬性。  

    "IncludeSearchResults":true

例如，若要建立只包含警示名稱和搜尋結果的自訂承載，您可以使用下列程式碼。 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


您可以在 [Log Analytics 警示 Webhook 範例](log-analytics-alerts-webhooks.md)使用 Webhook 來啟動外部服務，逐步執行建立警示規則的完整範例。

### <a name="runbook-actions"></a>Runbook 動作
Runbook 動作可在 Azure 自動化中啟動 Runbook。  若要使用這類型的動作，您必須在 OMS 工作區中安裝並設定 [自動化解決方案](log-analytics-add-solutions.md) 。  如果在建立新的警示規則時尚未安裝該解決方案，則會顯示其安裝的連結。  您可以從自動化解決方案中設定的自動化帳戶中的 Runbook 進行選取。

Runbook 動作會使用 [Webhook](../automation/automation-webhooks.md)來啟動 Runbook。  當您建立警示規則時，系統會自動為 Runbook 建立新的 Webhook，其名稱為 **OMS 警示補救** 後面接著 GUID。  

您無法直接填入 Runbook 的任何參數，但 [$WebhookData 參數](../automation/automation-webhooks.md) 會包含警示的詳細資料，包括記錄檔搜尋 (建立此警示) 的結果。  Runbook 必須定義 **$WebhookData** 做為參數，才能存取警示的屬性。  在 **$WebhookData** 的 **RequestBody** 屬性中，警示資料以 JSON 格式儲存在一個稱為 **SearchResults** 的屬性中。  此資料具有下表中的屬性。

| 節點 | 說明 |
|:--- |:--- |
| id |搜尋的路徑和 GUID。 |
| __metadata |警示的相關資訊，包括搜尋結果的記錄數和狀態。 |
| value |搜尋結果中每一筆記錄的個別項目。  項目的詳細資料會符合記錄的屬性和記錄。 |

例如，下列 Runbook 會擷取記錄搜尋所傳回的記錄，並根據每一筆記錄的類型來指派不同屬性。  請注意，Runbook 會開始從 JSON 轉換 **RequestBody**，讓它可以在 PowerShell 中當作物件來使用。

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>警示記錄
在 Log Analytics 中由警示規則建立的警示記錄，**Type** 為 **Alert**，**SourceSystem** 為 **OMS**。  其屬性如下表所示。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*警示* |
| SourceSystem |*OMS* |
| AlertSeverity |警示的嚴重性層級。 |
| AlertName |警示的名稱。 |
| 查詢 |已執行的查詢文字。 |
| QueryExecutionEndTime |查詢的時間範圍結尾。 |
| QueryExecutionStartTime |查詢的時間範圍開頭。 |
| TimeGenerated |建立警示的日期和時間。 |

[警示管理解決方案](log-analytics-solution-alert-management.md)和 [Power BI 匯出](log-analytics-powerbi.md)會建立其他種類的警示記錄。  這些記錄的 **Type** 均為 **Alert**，但依其 **SourceSystem** 區分。

## <a name="next-steps"></a>後續步驟
* 安裝 [警示管理解決方案](log-analytics-solution-alert-management.md) ，以分析在 Log Analytics 中建立的警示以及從 System Center Operations Manager (SCOM) 收集的警示。
* 深入了解可產生警示的 [記錄檔搜尋](log-analytics-log-searches.md) 。
* 完成 [設定 Webook](log-analytics-alerts-webhooks.md) 和警示規則的逐步解說。  
* 了解如何在 [Azure 自動化中撰寫 Runbook](https://azure.microsoft.com/documentation/services/automation) 以補救警示所識別的問題。

<!--HONumber=Oct16_HO2-->


