---
title: "適用於 Azure Functions App 的自動化資源部署 | Microsoft Docs"
description: "了解如何建置能部署 Azure Functions App 的 Azure Resource Manager 範本。"
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
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>適用於 Azure Functions App 的自動化資源部署

您也可以使用 Azure Resource Manager 範本來部署 Azure Functions App。 了解如何定義 Azure Functions App 所需的資源基準，以及於部署期間指定的參數。 根據您 Functions App 的[觸發程序和繫結](functions-triggers-bindings.md)，若要成功取得應用程式的「基礎結構即程式碼」組態，您可能需要部署額外的資源。

如需關於建立範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

如需完整範本的範例，請參閱[建立以取用方案為基礎的 Azure Functions App (英文)](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) 和[建立以 App Service 方案為基礎的 Azure Functions App (英文)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)。

## <a name="required-resources"></a>所需資源

您可以使用本文中的範例來建立基準 Azure Functions App。 針對 App，您需要以下資源：

* [Azure 儲存體](../storage/index.md)帳戶
* 主控方案 (取用方案或 Azure App Service 方案)
* Functions App (`type`: **Microsoft.Web/Site**, `kind`: **functionapp**)

## <a name="parameters"></a>參數

您可以使用 Azure Resource Manager 來定義您想要在部署範本時指定之值的參數。 範本的 **Parameters** 區段具有所有參數值。 針對會隨著您正在部署的專案或要部署到的環境而變化的值定義參數。

[變數](../azure-resource-manager/resource-group-authoring-templates.md#variables)適用於不會根據個別部署變更的值，或適用於在範本中使用之前需要轉換的參數 (例如，以順利通過驗證規則)。

在定義參數時，請使用 [**allowedValues**] 欄位來指定使用者可以在部署期間提供的值。 如果在部署期間未提供任何值，請使用 [**defaultValue**] 欄位來指派參數值。

Azure Resource Manager 範本會使用下列參數。

### <a name="appname"></a>appName

您想要建立的 Azure Functions App 名稱。

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

部署 Functions App 的位置。

> [!NOTE]
> 若要繼承 Resource Group 的位置，或是在 PowerShell 或 Azure CLI 部署期間沒有指定參數值的情況下，請使用 **defaultValue** 參數。 如果您是從 Azure 入口網站部署 App，請從 **allowedValues** 參數下拉式清單方塊中選取值。

> [!TIP]
> 如需可以使用 Azure Functions 的最新區域清單，請造訪[依區域提供的產品](https://azure.microsoft.com/regions/services/)。

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
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (選用)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>變數

Azure Resource Manager 範本會使用變數來合併參數，讓您可以在範本中使用更加詳細的設定。

在下一個範例中，為了符合 Azure 儲存體帳戶的[命名需求](../storage/storage-create-storage-account.md#create-a-storage-account)，我們會使用變數來套用 [Azure Resource Manager 範本函數](../azure-resource-manager/resource-group-template-functions.md)，以將輸入的 **appName** 值轉換成小寫。

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>要部署的資源

### <a name="storage-account"></a>儲存體帳戶

Azure Functions App 需要 Azure 儲存體帳戶。

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

### <a name="hosting-plan-consumption-vs-app-service"></a>主控方案：取用方案與App Service 方案

在某些案例中，您可能會想要讓函數由平台視需要做出調整，這也被稱為完全管理調整 (透過使用取用主控方案)。 或者，您可以會針對函數選擇使用者管理調整。 在使用者管理調整中，您的函數會在專用硬體上全天候執行 (透過使用 App Service 主控方案)。 執行個體數目可以透過手動或自動設定。 您對主控方案的選擇，可能是根據該方案所提供的功能，或是根據建構的成本。 若要深入了解主控方案，請參閱[調整 Azure Functions](functions-scale.md)。

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

### <a name="functions-app-a-site"></a>Functions App (網站)

在您選取調整選項之後，請建立 Functions App。 該 App 將會是保存您所有函數的容器。

Functions App 有許多子資源可供您用於部署，包括應用程式設定和原始檔控制選項。 您也可以選擇移除 **sourcecontrols** 子資源並改為使用不同的[部署選項](functions-continuous-deployment.md)。

> [!IMPORTANT]
> 若要透過使用 Azure Resource Manager 為應用程式建立成功的「基礎結構即程式碼」組態，請務必了解資源在 Azure 中部署的方式。 在下列範例中，將使用 **siteConfig** 套用高層級組態。 請務必將這些組態設定為高層級，因為它們會將資訊傳遞給 Azure Functions 執行階段和部署引擎。 在套用子 **sourcecontrols/web** 資源之前，需要高層級資訊。 雖然也可以在子層級 **config/appSettings** 資源設定這些設定，在某些案例下，您的 Functions App 及函數需在套用 **config/appSettings**「之前」完成部署。 在那些情況下 (例如在 [Logic Apps](../logic-apps/index.md) 中)，您的函數為另一個資源的相依性。

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
> 此範本使用[專案 (英文)](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 應用程式設定值，它能設定「函數部署引擎」(Kudu) 在其中尋找可部署程式碼的基礎目錄。 在我們的存放庫中，我們的函數是位於 **src** 資料夾的子資料夾中。 因此，在上述範例中，我們將應用程式設定值設定為 `src`。 如果您的函數位於您存放庫的根，或您並非從來源控制項進行部署，您可以移除此應用程式設定值。

## <a name="deploy-your-template"></a>部署您的範本

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure 入口網站](../azure-resource-manager/resource-group-template-deploy-portal.md)
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

深入了解如何開發並設定 Azure Functions。

* [Azure Functions 開發人員參考](functions-reference.md)
* [如何設定 Azure Functions 應用程式設定](functions-how-to-use-azure-function-app-settings.md)
* [建立您的第一個 Azure 函式](functions-create-first-azure-function.md)



<!--HONumber=Feb17_HO1-->


