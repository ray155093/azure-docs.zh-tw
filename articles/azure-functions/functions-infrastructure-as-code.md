---
title: "Azure Functions 的自動化資源部署 | Microsoft Docs"
description: "了解如何建置 Azure Resource Manager 範本，將函數應用程式部署至 Microsoft Azure。"
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, 函數, 無伺服器架構, 基礎結構即程式碼, azure resource manager"
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>自動化部署 Azure Functions 應用程式資源

在本主題中，您將學習如何建置 Azure Resource Manager 範本，以部署函數應用程式。 您將了解如何定義 Azure 函數和執行部署時指定的參數所需資源的基準。 根據函數中使用的[觸發程序和繫結](functions-triggers-bindings.md)，您可能需要部署額外的資源，以包含整個應用程式當成基礎結構即程式碼。

如需關於建立範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)

如需完整範本的範例，請至：[建立以使用量為基礎的 Azure 函數](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json)和/或[建立以 App Service 方案基礎的 Azure 函數](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)

## <a name="what-is-deployed"></a>部署內容

使用下列範例，您將會建立基準的 Azure 函數應用程式。 函數應用程式所需的資源如下︰

* [Azure 儲存體](../storage/index.md)帳戶
* 主控方案 (取用方案或 App Service 方案)
* 函數應用程式 (**functionapp** 種類的 Microsoft.Web/Site)

## <a name="parameters"></a>參數

透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。 此範本有一個 Parameters 區段，內含所有參數值。 您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。

[變數](../azure-resource-manager/resource-group-authoring-templates.md#variables)適用於不能根據個別部署變更的值，或適用於在範本中使用之前需要轉換的參數 (例如，要通過驗證規則)。

在定義參數時，請使用 [ **allowedValues** ] 欄位來指定使用者可以在部署期間提供的值。 使用 [ **defaultValue** ] 欄位來指派部署期間未提供任何值時的參數值。

讓我們來說明一下範本的參數。

### <a name="appname"></a>應用程式名稱

想要建立之函數的名稱。

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

要部署函數應用程式的位置。

> [!NOTE]
> **defaultValue** 參數可用來繼承資源群組的位置，或未在 Powershell 或 CLI 部署期間指定參數值時使用。 如果從入口網站部署，會提供下拉式方塊以從 **allowedValues** 中選擇。

> [!TIP]
> 如需提供 Azure Functions 的最新區域清單，請造訪[依區域提供的產品](https://azure.microsoft.com/regions/services/)頁面。

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL (選用)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch (選用)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (選用)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>變數

除了參數之外，Azure Resource Manager 範本也有參數概念，將變數併入以建置更特定的設定用於您的範本。

在下面這個範例中，您可以看到我們利用變數，套用 [Azure Resource Manager 範本函數](../azure-resource-manager/resource-group-template-functions.md)，取得提供的 appName 並轉換成小寫，以確保符合 Azure 儲存體帳戶的[命名需求](../storage/storage-create-storage-account.md#create-a-storage-account)。

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>要部署的資源

### <a name="storage-account"></a>儲存體帳戶

Azure 儲存體帳戶是 Azure Functions 所需的資源。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>主控方案：取用方案與 App Service 方案

比方說，您在建置函數時，希望函數能全面管理調整大小意義，依平台 (耗用量) 隨需求調整大小。 或者，您可以選擇使用者管理的調整大小 ，在可手動或自動設定數個執行個體的專用硬體 (App Service 方案) 上，全天候執行您的函數。 可根據方案中的可用功能，來決定是否要透過另一方案執行某一方案，或透過成本架構來決定。 若要深入了解不同的主控方案，請參閱[調整 Azure Functions](functions-scale.md)。

#### <a name="consumption-plan"></a>取用方案

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>App Service 方案

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="function-app-site"></a>函數應用程式 (網站)

選取調整選項之後，就可以建立容器來保存所有函數，這稱為「函數應用程式」。

函數應用程式有許多子資源可供您利用，包括「應用程式設定」和「原始檔控制選項」。 您可以選擇移除 **sourcecontrols** 子資源以利採用另一個[部署選項](functions-continuous-deployment.md)。

> [!IMPORTANT]
> 請務必了解如何在 Azure 中部署資源，確保您可在使用 Azure Resource Manager 時，為應用程式建立成功的基礎結構即程式碼組態。 在此範例中，您會注意到有最上層組態是使用 **siteConfig** 套用，在將意義傳遞到套用子資源 **sourcecontrols/web** 之前所需的 Azure Functions 執行階段和部署引擎時，這些務必要在最上層設定。 雖然可在子層級資源 **config/appSettings** 設定這些設定，但有些情況下需要「先」部署您的函數應用程式與函數，再套用 **config/appsettings**，因為您的函數依存於另一個資源，例如[邏輯應用程式](../logic-apps/index.md)。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 在此範本中，使用[專案](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file)應用程式設定，設定「函數部署引擎」(Kudu) 將在其中尋找可部署程式碼的基礎目錄。 在此範例中，我們將此值設定為 `src`，因為我們的函數是 GitHub 儲存機制所包含 `src` 資料夾的子項。 如果您的儲存機制是將函數直接放在儲存機制根目錄下，或您尚未從「原始檔控制」部署，則會移除此「應用程式設定」。

## <a name="deploying-your-template"></a>部署範本

* [Powershell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [入口網站](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>部署至 Azure 按鈕

以 GitHub 中 `azuredeploy.json` 檔案的原始路徑 [URL 編碼](https://www.bing.com/search?q=url+encode)版本取代 ```<url-encoded-path-to-azuredeploy-json>```。

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>後續步驟

現在，您可以從程式碼部署函數應用程式，利用這個機會深入了解如何開發和設定 Azure Functions︰

* [Azure Functions 開發人員參考](functions-reference.md)
* [如何設定 Azure Functions 應用程式設定](functions-how-to-use-azure-function-app-settings.md)
* [建立您的第一個 Azure 函式](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


