<properties
	pageTitle="使用跨平台命令列介面 (CLI) 建立 Azure 服務的警示 | Microsoft Azure"
	description="使用命令列介面建立 Azure 警示，在符合指定條件時觸發通知或自動化。"
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="robb"/>

# 使用跨平台命令列介面 (CLI) 建立 Azure 服務的警示 

> [AZURE.SELECTOR]
- [入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## Overview

本文將說明如何使用命令列介面 (CLI) 設定 Azure 的警示。

您可以收到以您 Azure 服務的監視計量或事件為基礎的警示。

- **計量值** - 當指定的計量值超出您在任一方向指派的臨界值時會觸發警示。也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。
- **活動記錄檔事件** - 警示可觸發「每一個」事件，或是僅在發生特定事件數目時才觸發。

您可以在警示觸發時，設定警示執行下列動作︰

- 傳送電子郵件給服務管理員和共同管理員
- 傳送電子郵件至您指定的其他電子郵件
- 呼叫 webhook
- 啟動執行 Azure Runbook (現階段只能從 Azure 入口網站啟動)

您可以透過下列方式設定和取得有關警示規則的資訊

- [Azure 入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [命令列介面 (CLI)](insights-alerts-command-line-interface.md)
- [Azure Insights REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


輸入命令並在結尾加上 -help，可以收到命令的說明。例如：

	azure insights alerts -help
	azure insights alerts actions email create -help


## 使用 CLI 建立警示規則

1. 執行必要條件，然後登入 Azure。請參閱 [Azure Insights CLI 範例](insights-cli-samples.md)。簡單地說，安裝 CLI，執行命令。這些命令可讓您登入、顯示您正在使用的訂用帳戶、準備執行 Insights 命令。


	```console
	azure login
	azure account show
	azure config mode arm 

	```

2.  若要列出資源群組中的現有規則，使用 **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;* 格式

	```console
	azure insights alerts rule list myresourcegroupname

	```
3. 若要建立規則，您需要先取得幾項重要資訊：
	- 您想要為其設定警示的**資源識別碼**
	- 該資源可使用的**計量定義**
	
    取得資源識別碼的方法之一，是使用 Azure 入口網站。假設已建立資源，在入口網站中選取它。然後在下一個刀鋒視窗中，選取 [設定] 區段下的 [屬性]。[資源識別碼] 是下一個刀鋒視窗中的欄位。另一種方法是使用 [Azure 資源總管](https://resources.azure.com/)。

    以下是 Web 應用程式的範例資源識別碼：
  
	```
	/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
	```

    若要取得先前資源範例中那些計量的可用計量和單位的清單，使用下列的 CLI 命令︰

	```console
	azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M 
  	```

	_PT1M_ 是可用度量單位的資料粒度 (間隔 1 分鐘)。使用不同的資料粒度可提供您不同的計量選項。

 
4. 若要建立以計量為基礎的警示規則，使用下列格式︰
 
	**azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*
	
	下列範例會設定網站資源的警示。只要持續 5 分鐘有收到任何流量，就會觸發警示，在這之後，5 分鐘沒收到任何流量也會觸發警示。

    ```console
	azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
   
	```

5. 若要在警示引發時建立 webhook 或傳送電子郵件，請先建立電子郵件和/或 webhook。然後立即建立規則。您無法使用 CLI 將 webhook 或電子郵件與已建立的規則建立關聯。
 
	```console
	azure insights alerts actions email create --customEmails myemail@contoso.com

	azure insights alerts actions webhook create https://www.contoso.com

	azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
	```


6. 若要建立活動記錄檔中特定條件發生時就會引發的警示，使用此格式︰
 
	**insights alerts rule log set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;operationName&gt;*

	例如

	```console
	azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
	```

    operationName 對應到活動記錄檔中事件類型的項目。*Microsoft.Compute/virtualMachines/delete* 和 *microsoft.insights/diagnosticSettings/write* 都是例子。

    您可以使用 PowerShell 命令 [Get AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) 取得 operationName 的可能值清單。或者，您可以使用 Azure 入口網站來查詢活動記錄檔，並尋找您想要建立其警示的特定過去作業。作業會顯示在名稱好記的圖形記錄檔檢視中。查看項目的 JSON，並拉出 OperationName 的值。

7. 您可以藉由查看個別規則來確認您的警示已正確建立。

	```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
	```

8. 若要刪除規則，使用此格式︰

	**insights alerts rule delete** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

	這些命令會刪除本文中先前建立的規則。

	```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
	```



## 後續步驟

* [取得 Azure 監視的概觀](monitoring-overview.md)中說明您可以收集和監視的資訊類型。
* 深入了解[在警示中設定 webhook](insights-webhooks-alerts.md)。
* 深入了解 [Azure 自動化 Runbook](..\automation\automation-starting-a-runbook.md)。
* 依照[收集診斷記錄檔概觀](monitoring-overview-of-diagnostic-logs.md)中的做法，收集您服務中詳細的高頻率計量。
* 依照[計量集合概觀](insights-how-to-customize-monitoring.md)中的做法，確保您的服務可使用且有回應。

<!---HONumber=AcomDC_0928_2016-->