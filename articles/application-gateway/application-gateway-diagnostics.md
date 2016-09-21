<properties 
   pageTitle="監視應用程式閘道的存取和效能記錄檔 | Microsoft Azure"
   description="了解如何啟用和管理應用程式閘道的存取和效能記錄檔"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# 應用程式閘道的診斷記錄功能

您可以在 Azure 中使用不同類型的記錄檔來管理和針對應用程式閘道進行疑難排解。透過入口網站可以存取這些當中的一些記錄檔，而從 Azure Blob 儲存體可以擷取所有記錄檔，並且可在不同的工具 (例如 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、Excel 及 PowerBI) 中檢視這些記錄檔。您可以從下列清單進一步了解不同類型的記錄檔。

- **稽核記錄檔︰**您可以使用 [Azure 稽核記錄檔](../azure-portal/insights-debugging-with-events.md) (之前稱為「作業記錄檔」) 來檢視提交至您的 Azure 訂用帳戶的所有作業及其狀態。預設會啟用稽核記錄檔，並可在 Azure Preview 入口網站中進行檢視。
- **存取記錄檔︰**您可以使用此記錄檔來檢視應用程式閘道存取模式及分析重要資訊，包括呼叫端的 IP、要求的 URL、回應延遲、傳回碼、輸入和輸出位元組。每隔 300 秒會收集一次存取記錄檔。此記錄檔包含每個應用程式閘道執行個體的一筆記錄。應用程式閘道執行個體可以由 'instanceId' 屬性識別。
- **效能記錄檔︰**您可以使用此記錄檔來檢視應用程式閘道執行個體的執行情況。此記錄檔會擷取每個執行個體的效能資訊，包括提供的要求總數、輸送量 (以位元組為單位)、提供的總要求數、失敗的要求計數、狀況良好和狀況不良的後端執行個體計數。每隔 60 秒會收集一次效能記錄檔。

>[AZURE.WARNING] 記錄檔僅適用於在資源管理員部署模型中部署的資源。您無法將記錄檔使用於傳統部署模型中的資源。若要深入了解這兩個模型，請參閱[了解資源管理員部署和傳統部署](../resource-manager-deployment-model.md)一文。

## 啟用記錄
每個 Resource Manager 資源都會自動啟用稽核記錄。您必須啟用存取和效能記錄功能，才能開始收集可透過這些記錄檔取得的資料。若要啟用記錄功能，請參閱下列步驟。

1. 請記下您的儲存體帳戶的資源識別碼 (記錄檔資料會儲存在其中)。其形式如下：/subscriptions/<subscriptionId>/resourceGroups/<資源群組名稱>/providers/Microsoft.Storage/storageAccounts/<儲存體帳戶名稱>。您訂用帳戶中的所有儲存體帳戶均可使用。您可以使用 Preview 入口網站來尋找此資訊。

	![Preview入口網站 - 應用程式閘道診斷](./media/application-gateway-diagnostics/diagnostics1.png)

2. 請記下您的應用程式閘道的資源識別碼 (將為其啟用記錄功能)。其形式如下：/subscriptions/<subscriptionId>/resourceGroups/<資源群組名稱>/providers/Microsoft.Network/applicationGateways/<應用程式閘道名稱>。您可以使用 Preview 入口網站來尋找此資訊。

	![Preview入口網站 - 應用程式閘道診斷](./media/application-gateway-diagnostics/diagnostics2.png)

3. 使用下列 Powershell Cmdlet 啟用診斷記錄功能。

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] 稽核記錄檔不需要個別的儲存體帳戶。將儲存體用於記錄存取和效能會產生服務費用。

## 稽核記錄檔

此記錄檔 (之前稱為「作業記錄檔」) 預設是由 Azure 產生。記錄檔會在 Azure 的 [事件記錄檔] 存放區中保留 90 天。閱讀[檢視事件和稽核記錄檔](../azure-portal/insights-debugging-with-events.md)一文，進一步了解這些記錄檔。

## 存取記錄檔

如果您已如上述步驟所述，對每個應用程式閘道進行啟用，才會產生此記錄檔。資料會儲存在您啟用記錄時所指定的儲存體帳戶中。應用程式閘道的每次存取會以 JSON 格式記錄下來，如下列範例所示。

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayAccess",
		"time": "2016-04-11T04:24:37Z",
		"category": "ApplicationGatewayAccessLog",
		"properties": {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIP":"37.186.113.170",
			"clientPort":"12345",
			"httpMethod":"HEAD",
			"requestUri":"/xyz/portal",
			"requestQuery":"",
			"userAgent":"-",
			"httpStatus":"200",
			"httpVersion":"HTTP/1.0",
			"receivedBytes":"27",
			"sentBytes":"202",
			"timeTaken":"359",
			"sslEnabled":"off"
		}
	}

## 效能記錄檔

如果您已如上述步驟所述，對每個應用程式閘道進行啟用，才會產生此記錄檔。資料會儲存在您啟用記錄時所指定的儲存體帳戶中。會記錄下列資料：

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayPerformance",
		"time": "2016-04-09T00:00:00Z",
		"category": "ApplicationGatewayPerformanceLog",
		"properties": 
		{
			"instanceId":"ApplicationGatewayRole_IN_1",
			"healthyHostCount":"4",
			"unHealthyHostCount":"0",
			"requestCount":"185",
			"latency":"0",
			"failedRequestCount":"0",
			"throughput":"119427"
		}
	}

## 檢視和分析稽核記錄檔

您可以使用下列任何方法，檢視和分析稽核記錄檔資料：

- **Azure 工具︰**透過 Azure PowerShell、Azure 命令列介面 (CLI)、Azure REST API 或 Azure Preview 入口網站，從稽核記錄擷取資訊。[稽核作業與資源管理員](../resource-group-audit.md)一文會詳述每個方法的逐步指示。
- **Power BI︰**如果還沒有 [Power BI](https://powerbi.microsoft.com/pricing) 帳戶，您可以免費試用。使用 [Power BI 的 Azure 稽核記錄檔內容套件](https://powerbi.microsoft.com/zh-TW/documentation/powerbi-content-pack-azure-audit-logs/)，您可以使用預先設定的儀表板 (可按原樣使用或加以自訂) 來分析資料。

## 檢視及分析存取和效能記錄檔

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 可以從您的 Blob 儲存體帳戶收集計數器和事件記錄檔，並且納入了視覺效果和強大的搜尋功能來分析您的記錄檔。

您也可以連接到儲存體帳戶並擷取存取和效能記錄檔的 JSON 記錄檔項目。下載 JSON 檔案後，您可以將它們轉換成 CSV 並在 Excel、PowerBI 或任何其他資料視覺化工具中檢視。

>[AZURE.TIP] 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 Github 所提供的[記錄檔轉換器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## 後續步驟

- 利用 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 以視覺化方式呈現計數器和事件記錄檔
- [使用 Power BI 視覺化您的 Azure 稽核記錄檔](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)部落格文章。
- [在 Power BI 和其他工具中檢視和分析 Azure 稽核記錄](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)部落格文章。

<!----HONumber=AcomDC_0907_2016-->