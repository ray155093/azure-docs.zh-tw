<properties
	pageTitle="Azure 診斷記錄檔概觀 | Microsoft Azure"
	description="認識 Azure 診斷記錄檔，並了解如何利用它們來了解 Azure 資源內發生的事件。"
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="johnkem"/>

# Azure 診斷記錄檔概觀
**Azure 診斷記錄檔**是由資源發出的記錄檔，提供有關該資源之作業的豐富、經常性資料。這些記錄檔的內容會因資源類型而不同 (例如，Windows 事件系統記錄檔是 VM、blob、資料表的診斷記錄檔中的一個類別，佇列記錄檔是儲存體帳戶的診斷記錄檔的類別)，而且和[活動記錄檔 (前身為稽核記錄或作業記錄)](monitoring-overview-activity-logs.md)不同，後者提供訂用帳戶中的資源上執行之作業的深入解析。並非所有資源皆支援此處所述的新型診斷記錄檔。文後的「支援的服務」一節會說明哪些資源類型支援新的診斷記錄檔。

![診斷記錄檔的邏輯位置](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## 診斷記錄檔的用途
以下是您可以利用診斷檔進行的事：

- 將診斷記錄檔儲存到**儲存體帳戶**以利稽核或手動檢查。您可以使用**診斷設定**指定保留時間 (以天為單位)。
- [將診斷記錄檔串流至**事件中樞**](monitoring-stream-diagnostic-logs-to-event-hubs.md)，以供第三方服務或自訂的分析解決方案 (如 PowerBI) 擷取。
- 以 [OMS Log Analytics](../log-analytics/log-analytics-azure-storage-json.md) 分析它們

## 診斷設定
非計算資源的診斷記錄檔要使用「診斷設定」來設定。用於資源控制的**診斷設定**：

- 診斷記錄檔傳送至何處 (儲存體帳戶、事件中樞和/或 OMS Log Analytics)。
- 傳送何種類別的記錄檔。
- 診斷記錄檔應該在儲存體帳戶中保留多久 – 保留期零天表示要永遠保留記錄檔。否則，此值範圍介於 1 到 2147483647 之間。如果有設定保留原則，但將儲存體帳戶的記錄檔儲存停用 (例如，如果只選取事件中樞或 OMS 選項)，保留原則不會有任何作用。

透過 Azure 入口網站中資源的 [診斷] 刀鋒視窗、透過 Azure PowerShell 和 CLI 命令、或是透過 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)，可以輕鬆地設定這些設定。

> [AZURE.WARNING] 計算資源 (例如，VM 或 Service Fabric) 的診斷記錄檔和度量使用[不同機制](../azure-diagnostics.md)來進行設定與選取輸出。

## 如何啟用診斷記錄檔的收集
您可以在建立資源的過程中啟用收集診斷記錄檔，或是在資源建立之後透過入口網站中資源的刀鋒視窗啟用收集。您也可以在任何時間點使用 Azure PowerShell 或 CLI 命令，或使用 Insights REST API 啟用診斷記錄檔。

> [AZURE.TIP] 這些指示可能無法直接套用於每個資源。請透過此頁面底部的結構描述連結，來了解適用於特定資源類型的特殊步驟。

[本文將說明如何使用資源範本在建立資源時啟用診斷設定](./monitoring-enable-diagnostic-logs-using-template.md)

### 在入口網站中啟用診斷記錄檔
當您透過下列方式建立某些資源類型時，您可以在 Azure 入口網站中啟用診斷記錄檔︰

1.	移至 [**新增**] 並選擇您感興趣的資源。
2.	設定基本設定並選取大小之後，在 [監視] 底下的 [設定] 刀鋒視窗中，選取 [啟用]，然後選擇您想要儲存診斷記錄檔的儲存體帳戶。將診斷傳送至儲存體帳戶時，您將需要支付儲存和交易的一般數據傳輸費用。

    ![在資源建立期間啟用診斷記錄檔](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.	按一下 [**確定**]，並建立資源。

若在資源建立之後要在 Azure 入口網站中啟用診斷記錄檔，可執行下列作業︰

1.	移至資源的刀鋒視窗，開啟 [診斷] 刀鋒視窗。
2.	按一下 [開啟] 並選取儲存體帳戶和/或事件中樞。

    ![在資源建立之後啟用診斷記錄檔](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.	在 [記錄] 下，選取您要收集或資料流哪些 [記錄檔分類]。
4.	按一下 [儲存]。

### 以程式設計方式啟用診斷記錄檔
若要透過 Azure PowerShell Cmdlet 啟用診斷記錄檔，請使用下列命令。

若要啟用儲存體帳戶中的診斷記錄檔的儲存體，使用下列命令︰

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

儲存體帳戶識別碼是您要將記錄檔傳送至此的儲存體帳戶的資源識別碼。

若要啟用將診斷記錄檔串流至事件中樞，使用下列命令︰

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

服務匯流排規則識別碼是此格式的字串︰`{service bus resource ID}/authorizationrules/{key name}`。

若要啟用將診斷記錄檔傳送到 Log Analytics 工作區，請使用此命令︰

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

您可以在 Azure 入口網站中取得 Log Analytics 工作區識別碼。

您可以結合這些參數讓多個輸出選項。

若要透過 Azure CLI 啟用診斷記錄檔，使用下列命令：

若要啟用儲存體帳戶中的診斷記錄檔的儲存體，使用下列命令︰

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

儲存體帳戶識別碼是您要將記錄檔傳送至此的儲存體帳戶的資源識別碼。

若要啟用將診斷記錄檔串流至事件中樞，使用下列命令︰

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

服務匯流排規則識別碼是此格式的字串︰`{service bus resource ID}/authorizationrules/{key name}`。

若要啟用將診斷記錄檔傳送到 Log Analytics 工作區，請使用此命令︰

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

您可以在 Azure 入口網站中取得 Log Analytics 工作區識別碼。

您可以結合這些參數讓多個輸出選項。

若要使用 Insights REST API 變更診斷設定，請參閱[這份文件](https://msdn.microsoft.com/library/azure/dn931931.aspx)。

## 在入口網站中管理診斷設定

為確保所有資源皆以診斷設定正確設定，您可以瀏覽至入口網站中的 [監視] 刀鋒視窗，並開啟 [診斷記錄檔] 刀鋒視窗。

![入口網站中的診斷記錄檔刀鋒視窗](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

您可能必須按一下 [更多服務] 來尋找 [監視] 刀鋒視窗。

在此刀鋒視窗中，您可以檢視及篩選支援診斷記錄檔的所有資源，以檢查它們是否已啟用診斷，以及該記錄檔要流向哪些儲存體帳戶、事件中樞及/或 Log Analytics 工作區。

![入口網站中的診斷記錄檔刀鋒視窗結果](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

按一下資源，即會顯示已儲存在儲存體帳戶的所有記錄檔，並提供您關閉或修改診斷設定的選項。按一下 [下載] 圖示來下載一段特定時間的記錄。

![一項資源的診斷記錄檔刀鋒視窗](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] 僅在您已設定診斷設定，將它們儲存到儲存體帳戶，診斷記錄檔才會出現在此檢視中並可供下載。

## 支援的服務以及診斷記錄檔的結構描述
診斷記錄檔的結構描述會根據資源和記錄類別而有所不同。以下是支援的服務和其結構描述。

| 服務 | 結構描述與文件 |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
| 軟體負載平衡器 | [Azure 負載平衡器的記錄檔分析 (預覽)](../load-balancer/load-balancer-monitor-log.md) |
| 網路安全性群組 | [網路安全性群組 (NSG) 的記錄檔分析](../virtual-network/virtual-network-nsg-manage-log.md) |
| 應用程式閘道 | [應用程式閘道的診斷記錄功能](../application-gateway/application-gateway-diagnostics.md) |
| 金鑰保存庫 | [Azure 金鑰保存庫記錄](../key-vault/key-vault-logging.md) |
| Azure 搜尋服務 | [啟用和使用搜尋流量分析](../search/search-traffic-analytics.md) |
| 資料湖存放區 | [存取 Azure Data Lake Store 的診斷記錄](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 資料湖分析 | [存取 Azure Data Lake Analytics 的診斷記錄](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| 邏輯應用程式 | 沒有可用的結構描述。 |
| Azure Batch | 沒有可用的結構描述。 |
| Azure 自動化 | 沒有可用的結構描述。 |

## 每個資源類型支援的記錄檔類別

|資源類型|類別|類別顯示名稱|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|作業記錄檔|
|Microsoft.Automation/automationAccounts|JobStreams|作業串流|
|Microsoft.Batch/batchAccounts|ServiceLog|服務記錄檔|
|Microsoft.DataLakeAnalytics/accounts|稽核|稽核記錄檔|
|Microsoft.DataLakeAnalytics/accounts|要求|要求記錄檔|
|Microsoft.DataLakeStore/accounts|稽核|稽核記錄檔|
|Microsoft.DataLakeStore/accounts|要求|要求記錄檔|
|Microsoft.KeyVault/vaults|AuditEvent|稽核記錄檔|
|Microsoft.Logic/workflows|WorkflowRuntime|工作流程執行階段診斷事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|網路安全性群組事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|網路安全性群組規則計數器|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|網路安全性群組規則流程事件|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|負載平衡器警示事件|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|負載平衡器探查健全狀況狀態|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|應用程式閘道存取記錄檔|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|應用程式閘道效能記錄檔|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|應用程式閘道防火牆記錄檔|
|Microsoft.Search/searchServices|OperationLogs|作業記錄|
|Microsoft.ServerManagement/nodes|RequestLogs|要求記錄檔|
|Microsoft.StreamAnalytics/streamingjobs|執行|執行|
|Microsoft.StreamAnalytics/streamingjobs|編寫|編寫|

## 後續步驟
- [將診斷記錄檔串流至**事件中樞**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [使用 Insights REST API 變更診斷設定](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [以 OMS Log Analytics 分析記錄檔](../log-analytics/log-analytics-azure-storage-json.md)

<!---HONumber=AcomDC_0928_2016-->