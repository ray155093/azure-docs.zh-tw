---
title: 監視應用程式閘道的存取和效能記錄檔與度量 | Microsoft Docs
description: 了解如何啟用和管理應用程式閘道的存取和效能記錄檔
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: amitsriva

---
# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>應用程式閘道的診斷記錄和度量
Azure 能夠讓您使用記錄和度量來監視資源

[**記錄**](#enable-logging-with-powershell) - 記錄可儲存效能、存取和其他記錄檔，或從資源取用以便進行監視。

[**度量**](#metrics) -應用程式閘道目前有一個度量。 此度量會測量應用程式閘道的輸送量，以每秒的位元組數為單位。

您可以在 Azure 中使用不同類型的記錄檔來管理和針對應用程式閘道進行疑難排解。 透過入口網站可以存取這些當中的一些記錄檔，而從 Azure Blob 儲存體可以擷取所有記錄檔，並且可在不同的工具 (例如 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、Excel 及 PowerBI) 中檢視這些記錄檔。 您可以從下列清單進一步了解不同類型的記錄檔：

* **稽核記錄檔︰** 您可以使用 [Azure 稽核記錄檔](../azure-portal/insights-debugging-with-events.md) (之前稱為「作業記錄檔」) 來檢視提交至您的 Azure 訂用帳戶的所有作業及其狀態。 預設會啟用稽核記錄檔，並可在 Azure Preview 入口網站中進行檢視。
* **存取記錄檔︰** 您可以使用此記錄檔來檢視應用程式閘道存取模式及分析重要資訊，包括呼叫端的 IP、要求的 URL、回應延遲、傳回碼、輸入和輸出位元組。 每隔 300 秒會收集一次存取記錄檔。 此記錄檔包含每個應用程式閘道執行個體的一筆記錄。 應用程式閘道執行個體可以由 'instanceId' 屬性識別。
* **效能記錄檔︰** 您可以使用此記錄檔來檢視應用程式閘道執行個體的執行情況。 此記錄檔會擷取每個執行個體的效能資訊，包括提供的要求總數、輸送量 (以位元組為單位)、提供的總要求數、失敗的要求計數、狀況良好和狀況不良的後端執行個體計數。 每隔 60 秒會收集一次效能記錄檔。
* **防火牆記錄檔︰** 您可以使用此記錄檔，檢視透過應用程式閘道的偵測或防止模式 (依 Web 應用程式防火牆的設定) 所記錄的要求。

> [!WARNING]
> 記錄檔僅適用於在資源管理員部署模型中部署的資源。 您無法將記錄檔使用於傳統部署模型中的資源。 若要深入了解這兩個模型，請參閱 [了解資源管理員部署和傳統部署](../resource-manager-deployment-model.md) 一文。
> 
> 

## <a name="enable-logging-with-powershell"></a>使用 PowerShell 啟用記錄
每個 Resource Manager 資源都會自動啟用稽核記錄。 您必須啟用存取和效能記錄功能，才能開始收集可透過這些記錄檔取得的資料。 若要啟用記錄功能，請參閱下列步驟： 

1. 請記下您的儲存體帳戶的資源識別碼 (記錄檔資料會儲存在其中)。 其形式如下：/subscriptions/\<subscriptionId\>/resourceGroups/\<資源群組名稱\>/providers/Microsoft.Storage/storageAccounts/\<儲存體帳戶名稱\>。 您訂用帳戶中的所有儲存體帳戶均可使用。 您可以使用 Preview 入口網站來尋找此資訊。
   
    ![Preview入口網站 - 應用程式閘道診斷](./media/application-gateway-diagnostics/diagnostics1.png)
2. 請記下您的應用程式閘道的資源識別碼 (將為其啟用記錄功能)。 其形式如下：/subscriptions/\<subscriptionId\>/resourceGroups/\<資源群組名稱\>/providers/Microsoft.Network/applicationGateways/\<應用程式閘道名稱\>。 您可以使用 Preview 入口網站來尋找此資訊。
   
    ![Preview入口網站 - 應用程式閘道診斷](./media/application-gateway-diagnostics/diagnostics2.png)
3. 使用下列 Powershell Cmdlet 啟用診斷記錄功能：
   
        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

> [AZURE.INFORMATION] 稽核記錄檔不需要個別的儲存體帳戶。 將儲存體用於記錄存取和效能會產生服務費用。
> 
> 

## <a name="enable-logging-with-azure-portal"></a>使用 Azure 入口網站啟用記錄
### <a name="step-1"></a>步驟 1
在 Azure 入口網站中瀏覽至您的資源。 按一下 [ **診斷記錄**]。 如果是第一次設定診斷，刀鋒視窗看起來如下圖︰

應用程式閘道可使用 3 個記錄檔。

* 存取記錄檔
* 效能記錄檔
* 防火牆記錄檔

按一下 [開啟診斷]  開始收集資料。

![診斷設定刀鋒視窗][1]

### <a name="step-2"></a>步驟 2
在 [診斷設定]  刀鋒視窗中，會設定診斷記錄檔的設定方式。 在此範例中，會使用 Log Analytics 來儲存記錄檔。 按一下 [Log Analytics] 底下的 [設定] 來設定您的工作區。 您也可以使用事件中樞和儲存體帳戶來儲存診斷記錄檔。

![診斷刀鋒視窗][2]

### <a name="step-3"></a>步驟 3
選擇現有的 OMS 工作區或建立新的。 這個範例會使用現有工作區。

![OMS 工作區][3]

### <a name="step-4"></a>步驟 4
完成時請確認設定，然後按一下 [儲存]  儲存設定。

![確認選取項目][4]

## <a name="audit-log"></a>稽核記錄檔
此記錄檔 (之前稱為「作業記錄檔」) 預設是由 Azure 產生。  記錄檔會在 Azure 的 [事件記錄檔] 存放區中保留 90 天。 閱讀 [檢視事件和稽核記錄檔](../azure-portal/insights-debugging-with-events.md) 一文，進一步了解這些記錄檔。

## <a name="access-log"></a>存取記錄檔
如果您已如上述步驟所述，對每個應用程式閘道進行啟用，才會產生此記錄檔。 資料會儲存在您啟用記錄時所指定的儲存體帳戶中。 應用程式閘道的每次存取會以 JSON 格式記錄下來，如下列範例所示：

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

## <a name="performance-log"></a>效能記錄檔
如果您已如上述步驟所述，對每個應用程式閘道進行啟用，才會產生此記錄檔。 資料會儲存在您啟用記錄時所指定的儲存體帳戶中。 會記錄下列資料：

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


## <a name="firewall-log"></a>防火牆記錄檔
如果您已如上述步驟所述，對每個應用程式閘道進行啟用，才會產生此記錄檔。 此記錄檔也需要在應用程式閘道上設定該 Web 應用程式防火牆。 資料會儲存在您啟用記錄時所指定的儲存體帳戶中。 會記錄下列資料：

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>檢視和分析稽核記錄檔
您可以使用下列任何方法，檢視和分析稽核記錄檔資料：

* **Azure 工具︰** 透過 Azure PowerShell、Azure 命令列介面 (CLI)、Azure REST API 或 Azure Preview 入口網站，從稽核記錄擷取資訊。  [稽核作業與資源管理員](../resource-group-audit.md) 一文會詳述每個方法的逐步指示。
* **Power BI︰** 如果還沒有 [Power BI](https://powerbi.microsoft.com/pricing) 帳戶，您可以免費試用。 使用 [Power BI 的 Azure 稽核記錄檔內容套件](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)，您可以使用預先設定的儀表板 (可按原樣使用或加以自訂) 來分析資料。

## <a name="view-and-analyze-the-access,-performance-and-firewall-log"></a>檢視及分析存取、效能和防火牆記錄檔
Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 可以從您的 Blob 儲存體帳戶收集計數器和事件記錄檔，並且納入了視覺效果和強大的搜尋功能來分析您的記錄檔。

您也可以連接到儲存體帳戶並擷取存取和效能記錄檔的 JSON 記錄檔項目。 下載 JSON 檔案後，您可以將它們轉換成 CSV 並在 Excel、PowerBI 或任何其他資料視覺化工具中檢視。

> [!TIP]
> 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 Github 所提供的 [記錄檔轉換器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter) 。
> 
> 

## <a name="metrics"></a>度量
度量是某些 Azure 資源的功能，可供您在入口網站中檢視效能計數器。 本文撰寫當下，應用程式閘道只有一個可用度量。 此度量是輸送量，可在入口網站中看到。 請瀏覽至應用程式閘道，然後按一下 [度量] 。  在 [可用的度量]  區段選取輸送量以檢視這些值。 您可以在下圖中看到一個範例，內有篩選器可用來顯示不同時間範圍的資料。

若要查看目前支援的度量清單，請瀏覽 [支援 Azure Monitor 的度量](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![度量檢視][5]

## <a name="alert-rules"></a>警示規則
警示規則可以根據資源度量來啟動。 這對於應用程式閘道的意義是，如果應用程式閘道的輸送量高於、低於或等於臨界值達到一段指定時間，警示便可以呼叫 Webhook 或寄送電子郵件給系統管理員。

下列範例會逐步引導您建立警示規則，以在達到輸送量臨界值之後傳送電子郵件給系統管理員。

### <a name="step-1"></a>步驟 1
按一下 [新增度量警示]  來開始。 您也可以透過 [度量] 刀鋒視窗來到達此刀鋒視窗。

![警示規則刀鋒視窗][6]

### <a name="step-2"></a>步驟 2
在 [新增規則] 刀鋒視窗中，填入名稱、條件和通知等區段，然後在完成時按一下 [確定]。

[條件] 選取器允許 4 個值，分別為**大於**、**大於或等於**、**小於**或**小於或等於**。

[期間]  選取器可用來挑選 5 分鐘到 6 小時的期間。

藉由選取 [傳送電子郵件給擁有者、參與者和讀者]  ，便可根據可存取該資源的使用者動態傳送電子郵件。 否則，您可以在 [其他系統管理員電子郵件]  文字方塊中提供以逗號分隔的使用者清單。

![新增規則刀鋒視窗][7]

如果達到臨界值，送達的電子郵件會類似下圖內容︰

![超過臨界值的電子郵件][8]

建立度量警示後就會顯示警示清單，並概述所有警示規則。

![警示規則檢視][9]

若要深入了解警示通知，請造訪 [接收警示通知](../azure-portal/insights-receive-alert-notifications.md)

若要深入了解 Webhook 以及其如何與警示搭配使用，請造訪 [針對 Azure 度量警示設定 Webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>後續步驟
* 利用 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 
* [使用 Power BI 視覺化您的 Azure 稽核記錄檔](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 部落格文章。
* [在 Power BI 和其他工具中檢視和分析 Azure 稽核記錄](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 部落格文章。

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png


<!--HONumber=Oct16_HO2-->


