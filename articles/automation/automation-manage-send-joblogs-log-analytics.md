<properties
    pageTitle="從「自動化」將工作狀態和工作資料流轉送到 Log Analytics (OMS) | Microsoft Azure"
    description="這篇文章示範如何將工作狀態和 Runbook 工作資料流傳送到 Microsoft Operations Management Suite Log Analytics，以提供額外的深入解析和管理。"
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="08/08/2016"
    ms.author="magoedte" />

# 從「自動化」將工作狀態和工作資料流轉送到 Log Analytics (OMS)

「自動化」可以將 Runebook 工作狀態和工作資料流傳送到您的 Microsoft Operations Management Suite (OMS) Log Analytics 工作區。雖然您可以在 Azure 入口網站中或是透過 PowerShell，以個別工作狀態或特定自動化帳戶所有工作的方式檢視此資訊，您必須建立自訂的 PowerShell 指令碼，才能取得支援作業需求的進階內容。現在透過 Log Analytics，您將可以：

- 針對自動化工作取得深入解析
- 根據您的 Runbook 工作狀態 (例如已失敗或已暫停) 觸發電子郵件或警示
- 撰寫工作資料流之間的進階查詢
- 在自動化帳戶之間相互關聯工作
- 視覺化一段時間的工作歷程記錄

## 先決條件和部署考量

若要開始將自動化記錄檔傳送到 Log Analytics，您必須擁有下列項目：

1. OMS 訂用帳戶。如需詳細資訊，請參閱[開始使用 Log Analytics](../log-analytics/log-analytics-get-started.md)。

    >[AZURE.NOTE]OMS 工作區和自動化帳戶必須位於相同的 Azure 訂用帳戶中，此設定才能正確運作。
  
2. [Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。
   
    >[AZURE.NOTE]儲存體帳戶「必須」與自動化帳戶位於相同的區域中。
 
3. Azure PowerShell 以及 Operational Insights Cmdlet 版本 1.0.8 或更新版本。如需有關此版本以及如何安裝的資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。
4. Azure 診斷和 Log Analytics PowerShell。如需有關此版本及安裝方式的詳細資訊，請參閱 [Azure Diagnostic and Log Analytics (Azure 診斷和 Log Analytics)](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1)。
5. 從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript)下載 PowerShell 指令碼 **Enable-AzureDiagnostics.ps1**。此指令碼將會設定以下內容：
 - 為您所指定的自動化帳戶保存 Runbook 工作狀態和資料流資料的儲存體帳戶。
 - 啟用從您的自動化帳戶收集此資料，以將它以 JSON 格式儲存在 Azure Blob 儲存體帳戶中。
 - 針對從您的 Blob 儲存體帳戶將資料收集到 OMS Log Analytics 進行設定。
 - 針對您的 OMS 工作區啟用自動化 Log Analytics 解決方案。

**Enable-AzureDiagnostics.ps1** 指令碼於執行期間需要下列參數：

- *AutomationAccountName* - 您的自動化帳戶名稱
- *LogAnalyticsWorkspaceName* - 您的 OMS 工作區名稱

若要尋找 *AutomationAccountName* 的值，請在 Azure 入口網站中，從 [自動化帳戶] 刀鋒視窗選取您的自動化帳戶，然後選取 [所有設定]。在 [所有設定] 刀鋒視窗中，選取 [帳戶設定] 之下的 [屬性]。在 [屬性] 刀鋒視窗中，您可以記下這些值。<br> ![自動化帳戶屬性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)。


## 使用 Log Analytics 進行設定整合

1. 在您的電腦上，從 [開始] 畫面啟動 **Windows PowerShell**。
2. 從 PowerShell 命令列殼層，瀏覽到包含已下載指令碼的資料夾，並執行該指令碼以變更參數 *-AutomationAccountName* 和 *-LogAnalyticsWorkspaceName* 的值。

    >[AZURE.NOTE] 執行指令碼之後，您會收到向 Azure 進行驗證的提示。您「必須」以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。
    
        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `

3. 執行這個指令碼之後，您應該可以在將診斷資料寫入儲存體之後約 30 分鐘，於 Log Analytics 中看見記錄。如果您無法在這個時間之後取得記錄，請參閱 [JSON files in blob storage (Blob 儲存體中的 JSON 檔案)](../log-analytics/log-analytics-azure-storage-json.md#troubleshooting-configuration-for-azure-diagnostics-written-to-blob-in-json) 中的疑難排解小節。

### 驗證組態

若要確認指令碼已成功設定您的自動化帳戶和 OMS 工作區，您可以在 PowerShell 中執行下列步驟。在您那樣做之前，若要尋找 OMS 工作區名稱和資源群組名稱的值，請在 Azure 入口網站中瀏覽到 [Log Analytics (OMS)]，並記下 [Log Analytics (OMS)] 刀鋒視窗中 [名稱] 和 [資源群組] 的值。<br> ![OMS Log Analytics 工作區清單](media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png) 我們會在使用 PowerShell Cmdlet [Get-AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/library/mt603567.aspx) 驗證您 OMS 工作區中的組態時使用這兩個值。

1.  從 Azure 入口網站，瀏覽到 [儲存體帳戶]，並搜尋使用下列命名慣例的儲存體帳戶 - *AutomationAccountNameomsstorage*。當 Runbook 工作完成之後，您應該很快便能看見兩個建立的 Blob 容器 - **insights-logs-joblogs** 和 **insights-logs-jobstreams**。

2.  從 PowerShell，執行下列 PowerShell 程式碼，以變更您先前複製或記下的參數 **ResourceGroupName** 和 **WorkspaceName** 值。

    Login-AzureRmAccount Get-AzureRmSubscription -SubscriptionName 'SubscriptionName' | Set-AzureRmContext Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "OMSResourceGroupName" ` -Workspace "OMSWorkspaceName"

    這將會傳回指定 OMS 工作區的儲存體深入解析。我們想要確認先前指定的自動化帳戶儲存體深入解析確實存在，而 **State** 物件顯示的值為 **OK**。<br> ![來自 Get-AzureRmOperationalInsightsStorageInsights Cmdlet 的結果](media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png)。

## 在 Log Analytics 中檢視自動化記錄檔 

既然您已經開始將自動化工作記錄傳送到 Log Analytics，我們來看看這些記錄檔在 OMS 中的運用方式。

### 在 Runbook 工作失敗或暫停時傳送電子郵件 

我們最常從客戶收到的其中一個問題，便是希望系統能在 Runbook 工作發生問題時，傳送電子郵件或簡訊以通知他們。

若要建立警示規則，首先針對應叫用警示的 Runbook 工作記錄，建立記錄檔搜尋。[警示] 按鈕將可供您使用，以便建立和設定警示規則。

1.	從 [OMS 概觀] 頁面，按一下 [記錄檔搜尋]。
2.	透過在查詢欄位中輸入下列內容來為您的警示建立記錄檔搜尋查詢：`Category=JobLogs (ResultType=Failed || ResultType=Suspended)`。您也可以透過使用下列內容來依 RunbookName 分組：`Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`。
  
    如果您已將來自多個自動化帳戶或訂用帳戶的記錄檔設定到您的工作區，您可能會想要以訂用帳戶或自動化帳戶將您的警示分組。自動化帳戶名稱可以衍生自 JobLogs 搜尋的 [資源] 欄位。

3.	按一下頁面頂端的 [警示] 以開啟 [新增警示規則] 畫面。如需設定警示選項的詳細資料，請參閱 [Log Analytics 中的警示](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule)。

### 尋找所有已完成但發生錯誤的工作 

除了根據失敗所做的警示之外，您應該也會想要在 Runbook 工作發生非終止錯誤 (PowerShell 產生錯誤資料流，但非終止錯誤並不會造成工作暫停或失敗) 時收到通知。

1. 在 OMS 入口網站中，按一下 [記錄檔搜尋]。
2. 在查詢欄位中，輸入 `Category=JobStreams StreamType_s=Error | measure count() by JobId_g`，然後按一下 [**搜尋**]。


### 檢視工作的工作資料流  

當您在針對工作進行偵錯時，您可能也會想要查看工作資料流。下列查詢會顯示單一工作具有 GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的所有資料流：

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### 檢視歷史工作狀態 

最後，您可能會想要視覺化一段時間的工作歷程記錄。您可以使用此查詢來搜尋您的工作在一段時間的狀態。

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day` <br> ![OMS 歷史工作狀態圖表](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## 總結

透過將您的自動化工作狀態和資料流資料傳送到 Log Analytics，您可以透過設定警示以在發生問題時做出通知，以及使用進階查詢自訂儀表板來視覺化 Runbook 結果、Runbook 工作狀態及其他相關的關鍵指示器或計量，來針對您的自動化工作取得更佳的深入解析。這將能協助提供更廣泛的作業可見性，並能更快速地解決各種事件。


## 後續步驟

- 若要深入了解如何建構不同的搜尋查詢，以及使用 Log Analytics 檢閱自動化工作記錄檔，請參閱 [Log Analytics 中的記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)
- 若要了解如何建立及擷取 Runbook 的輸出與錯誤訊息，請參閱 [Runbook 輸出與訊息](automation-runbook-output-and-messages.md)
- 若要深入了解 Runbook 執行方式、如何監視 Runbook 工作，以及其他技術性詳細資料，請參閱[追蹤 Runbook 工作](automation-runbook-execution.md)
- 若要深入了解 OMS Log Analytics 和資料收集來源，請參閱[在 Log Analytics 中收集 Azure 儲存體資料概觀](../log-analytics/log-analytics-azure-storage.md)

<!---HONumber=AcomDC_0810_2016---->