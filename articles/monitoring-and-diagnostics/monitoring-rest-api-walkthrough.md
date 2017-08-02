---
title: "Azure 監視 REST API 逐步解說 | Microsoft Docs"
description: "如何驗證 Azure 監視 REST API 的要求，並使用 Azure 監視 REST API。"
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: mcollier
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 454a85c4752ec9c7522ef147d5ce594ef5992c32
ms.contentlocale: zh-tw
ms.lasthandoff: 07/18/2017

---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure 監視 REST API 逐步解說
本文將說明如何執行驗證，讓您的程式碼可以使用 [Microsoft Azure 監視器 REST API 參考](https://msdn.microsoft.com/library/azure/dn931943.aspx)。         

Azure 監視器 API 讓您能夠以程式設計方式擷取可用的預設度量定義 (例如 CPU 時間、要求等度量類型)、資料粒度和度量值。 一旦擷取之後，資料就可以儲存於個別的資料存放區，例如 Azure SQL Database、Azure Cosmos DB 或 Azure Data Lake。 其他分析可視需要從該處執行。

除了使用不同的度量資料點之外，如本文所示範，監視 API 讓您能夠列出警示規則、檢視活動記錄檔等等。 如需可用作業的完整清單，請參閱 [Microsoft Azure 監視器 REST API 參考](https://msdn.microsoft.com/library/azure/dn931943.aspx)。

## <a name="authenticating-azure-monitor-requests"></a>驗證 Azure 監視器要求
第一步是驗證要求。

針對 Azure 監視器 API 執行的所有工作都會使用 Azure Resource Manager 驗證模型。 因此，所有要求都必須使用 Azure Active Directory (Azure AD) 進行驗證。 驗證用戶端應用程式的其中一個方法是建立 Azure AD 服務主體，並擷取驗證 (JWT) 權杖。 下列範例指令碼會示範透過 PowerShell 建立 Azure AD 服務主體的方法。 如需更詳細的逐步解說中，請參閱 [使用 Azure PowerShell 建立用來存取資源的服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password)上的文件。 它也可以[透過 Azure 入口網站建立服務原則](../azure-resource-manager/resource-group-create-service-principal-portal.md)。

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

若要查詢 Azure 監視器 API，用戶端應用程式應使用先前建立的服務主體來進行驗證。 下列範例 PowerShell 指令碼使用 [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) 說明其中一個方法，協助取得 JWT 驗證權杖。 JWT 權杖會做為要求中 HTTP 授權參數的一部分傳遞至 Azure 監視器 API。

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

一旦驗證設定步驟完成後，即可針對 Azure 監視器 REST API 執行查詢。 有兩個實用的查詢︰

1. 列出資源的度量定義
2. 擷取度量值

## <a name="retrieve-metric-definitions"></a>擷取度量定義
> [!NOTE]
> 若要使用 Azure 監視器 REST API 擷取度量定義，請使用 "2016-03-01" 做為 API 版本。
>
>

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
若為 Azure 邏輯應用程式，度量定義會出現類似下列的螢幕擷取畫面︰

![替代「度量定義回應的 JSON 檢視。」](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

如需詳細資訊，請參閱 [列出 Azure 監視器 REST API 中的資源度量定義](https://msdn.microsoft.com/library/azure/mt743621.aspx) 文件。

## <a name="retrieve-metric-values"></a>擷取度量值
一旦已知可用的度量定義後，便可擷取相關的度量值。 將度量名稱 ‘value’ (而非 ‘localizedValue’) 用於任何篩選要求 (例如，擷取 ‘CpuTime’ 和 ‘Requests’ 度量資料點)。 如果未指定篩選器，則會傳回預設度量。

> [!NOTE]
> 若要使用 Azure 監視器 REST API 擷取度量值，請使用 "2016-06-01" 做為 API 版本。
>
>

**方法**：GET

**要求 URI**：https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*

例如，若要擷取給定時間範圍及時間粒紋為 1 小時的 RunsSucceeded 度量資料點，要求如下所示︰

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

結果隨即出現，類似下列螢幕擷取畫面範例︰

![替代「顯示平均回應時間度量值的 JSON 回應」](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

若要擷取多個資料或彙總點，將度量定義名稱和彙總類型新增至篩選器，如下列範例所示︰

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>使用 ARMClient
使用 PowerShell (如上所示) 的替代方案，是使用 Windows 電腦上的 [ARMClient](https://github.com/projectkudu/ARMClient) 。 ARMClient 會自動處理 Azure AD 驗證 (以及產生的 JWT 權杖)。 下列步驟概述使用 ARMClient 來擷取度量資料︰

1. 安裝 [Chocolatey](https://chocolatey.org/) 和 [ARMClient](https://github.com/projectkudu/ARMClient)。
2. 在終端機視窗中，輸入 armclient.exe login 。 這會提示您登入 Azure。
3. 輸入 *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. 輸入 *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01*

![替代「使用 ARMClient 來使用 Azure 監視 REST API」](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)

## <a name="retrieve-the-resource-id"></a>擷取資源識別碼
使用 REST API 可實際協助了解可用的度量定義、細微度以及相關值。 使用 [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx)時，該資訊會很實用。

上述程式碼中，要使用的資源識別碼是所需 Azure 資源的完整路徑。 例如，若要查詢 Azure Web 應用程式，資源識別碼為︰

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

下列範例清單包含各種 Azure 資源的資源識別碼格式︰

* **IoT 中樞** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **SQL 彈性集區** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **服務匯流排** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **VM 擴展集** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VM** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **事件中樞** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

還有其他方法可擷取資源識別碼，包括使用 Azure 資源總管、在 Azure 入口網站中以及透過 PowerShell 或 Azure CLI 檢視所需的資源。

### <a name="azure-resource-explorer"></a>Azure 資源總管
若要尋找所需資源的資源識別碼，其中一個實用的方法是使用 [Azure 資源總管](https://resources.azure.com) 工具。 瀏覽至所需的資源，然後查看如下列螢幕擷取畫面所示的識別碼︰

![替代「Azure 資源總管」](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure 入口網站
也可以從 Azure 入口網站取得資源識別碼。 若要這樣做，請瀏覽至所需的資源，然後選取 [內容]。 資源識別碼會顯示在 [屬性] 刀鋒視窗中，如下列螢幕擷取畫面所示︰

![替代「Azure 入口網站的 [屬性] 刀鋒視窗中顯示的資源識別碼」](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
可以使用 Azure PowerShell Cmdlet 來擷取資源識別碼。 例如，若要取得 Azure Web 應用程式的資源識別碼，請執行 Get-AzureRmWebApp Cmdlet，如下列螢幕擷取畫面所示︰

![替代「透過 PowerShell 取得的資源識別碼」](./media/monitoring-rest-api-walkthrough/resourceid_powershell.png)

### <a name="azure-cli"></a>Azure CLI
若要使用 Azure CLI 擷取資源識別碼，請執行 'azure webapp show' 命令，指定 '-json' 選項，如下列螢幕擷取畫面所示︰

![替代「透過 PowerShell 取得的資源識別碼」](./media/monitoring-rest-api-walkthrough/resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>擷取活動記錄檔資料
除了使用度量定義及相關值，也可以擷取關於 Azure 資源的其他有趣深入見解。 例如，可以查詢 [活動記錄檔](https://msdn.microsoft.com/library/azure/dn931934.aspx) 資料。 下列範例示範使用 Azure 監視器 REST API 查詢 Azure 訂用帳戶特定日期範圍內的活動記錄檔資料︰

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>後續步驟
* 檢閱 [監視的概觀](monitoring-overview.md)。
* 檢視 [支援 Azure 監視器的度量](monitoring-supported-metrics.md)。
* 檢閱 [Microsoft Azure 監視器 REST API 參考](https://msdn.microsoft.com/library/azure/dn931943.aspx)。
* 檢閱 [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx)。

