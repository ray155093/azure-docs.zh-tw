<properties 
	pageTitle="Logic Apps 的定價模式 | Microsoft Azure" 
	description="有關 Logic Apps 定價情形的詳細資料" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/27/2016"
	ms.author="klam"/>

# Logic Apps 的定價模式

Azure Logic Apps 可讓您在雲端中執行整合工作流程和調整其規模。以下是 Logic Apps 計費和定價方案的詳細資料。

## 取用定價

新建立的 Logic Apps 會使用取用方案。使用 Logic Apps 的取用定價模式時，您只需針對使用的部分付費。使用取用方案時，Logic Apps 不會執行節流。在執行邏輯應用程式執行個體時所執行的所有動作都會計量。

### 什麼是動作執行？

邏輯應用程式定義的每一個步驟都是一個動作。這包括觸發程序、控制流程步驟 (如條件、範圍、for each 迴圈、do until 迴圈、呼叫連接器和呼叫原生動作)。觸發程序就是設計用來在特定事件發生時，具現化新的邏輯應用程式執行個體的特殊動作。觸發程序擁有一些會影響邏輯應用程式計量方式的不同行為。

-	**輪詢觸發程序** – 此觸發程序會持續輪詢端點，直到它收到的訊息符合用來建立新的邏輯應用程式執行個體的準則。在 Logic Apps 設計工具中，可於觸發程序內設定輪詢間隔。即使是未建立新的邏輯應用程式執行個體的輪詢要求也會計算為一個動作執行。

-	**Webhook 觸發程序** – 此觸發程序會等候用戶端送來針對特定端點的要求。每個傳送至 Webhook 端點的要求都會計算為一個動作執行。要求觸發程序和 HTTP Webhook 觸發程序皆為 Webhook 觸發程序。

-	**循環觸發程序** – 此觸發程序會根據觸發程序中所設定的循環間隔建立新的邏輯應用程式執行個體。例如，您可以將循環觸發程序設定為每 3 天執行一次，或甚至是每分鐘執行一次。

在 [Logic Apps 資源] 刀鋒視窗的 [觸發程序記錄] 部分可以看到觸發程序執行。

所有已執行的動作不論成功或失敗，都為計量為動作執行。因為條件不符而略過的動作，或因為邏輯應用程式在完成之前就終止而未執行的動作，均不會計算為一個動作執行。

在迴圈內執行的動作每反覆運算一次就會計算一次。例如，for each 迴圈內會針對 10 個項目所組成的清單逐一執行一遍的單一動作，將會計算為清單項目數 (10) 乘以迴圈中的動作數目 (1) 再加上起始迴圈時的一次執行，因此得出此範例共有 (10 * 1) + 1 = 11 個動作執行。

已停用的 Logic Apps 不能具現化新的執行個體，因此在其停用期間內將不會收費。請注意，在停用邏輯應用程式之後，執行個體可能要先花點時間停止，然後才能完全停用。

## App Service 方案

建立邏輯應用程式時不再需要 App Service 方案。您也可以參考具有現有邏輯應用程式的 App Service 方案。先前使用 App Service 方案所建立的邏輯應用程式會繼續保持之前的行為方式，而根據您選擇的方案而定，其將會在超過一定的每日執行次數時遭到節流，但不會使用動作執行計量器進行計費。

App Service 方案和其每日允許的動作執行次數︰

| |免費/共用/基本|標準|高級|
|---|---|---|---|
|每日的動作執行次數| 200|10,000|50,000|

### 從取用定價轉換為 App Service 方案定價

若要為取用邏輯應用程式參考 App Service 方案，您只需要[執行下列 PowerShell 指令碼](https://github.com/logicappsio/ConsumptionToAppServicePlan)。請確定您已先安裝 [Azure PowerShell 工具](https://github.com/Azure/azure-powershell)。

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
	[string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### 從 App Service 方案定價轉換為取用定價

若要將具有相關聯 App Service 方案的邏輯應用程式變更為取用模式，請在邏輯應用程式定義中移除 App Service 方案的參考。呼叫 PowerShell Cmdlet 即可完成此作業︰

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## 價格

如需定價詳細資料，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

## 後續步驟

- [Logic Apps 概觀][whatis]
- [建立第一個邏輯應用程式][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0810_2016---->