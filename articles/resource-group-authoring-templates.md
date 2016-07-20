<properties
   pageTitle="撰寫 Azure Resource Manager | Microsoft Azure"
   description="使用宣告式 JSON 語法建立 Azure 資源管理員範本，藉此將應用程式部署至 Azure。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# 編寫 Azure 資源管理員範本

在 Azure Resource Manager 範本中，您會定義要部署給解決方案的資源，以及指定參數和變數，讓您可以針對不同的環境輸入值。範本由 JSON 與運算式所組成，可讓您用來為部署建構值。此主題說明範本的各區段。

Visual Studio 提供工具來協助您建立範本。如需有關如何以您的範本使用 Visual Studio 的詳細資訊，請參閱[透過 Visual Studio 建立和部署 Azure 資源群組](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

如需建立範本的指導方針，請參閱[資源管理員範本逐步解說](resource-manager-template-walkthrough.md)。

## 規劃您的範本

開始使用範本之前，您應該花一些時間來找出您想要部署的資源以及使用範本的方式。具體來說，您應該考慮：

1. 您需要部署哪些資源類型
2. 這些資源所在的位置
3. 部署資源時您將使用的資源提供者 API 版本
4. 是否有任何資源必須部署在其他資源之後
5. 您想要在部署期間傳入的值，以及您想要在範本中直接定義的值
6. 您是否需要從部署傳回值

若找出哪些資源類型可供部署、各類型支援的區域，以及每個類型可用的 API 版本，請參閱[資源管理員提供者、區域、API 版本及結構描述](resource-manager-supported-services.md)。

您必須將範本大小限制為 1 MB，並將每個參數檔案限制為 64 KB。1 MB 的限制適用於已增加反覆資源定義和變數和參數值之範本的最終狀態。

## 範本格式

在最簡單的結構中，範本包含下列元素。

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| 元素名稱 | 必要 | 說明
| :------------: | :------: | :----------
| $schema | 是 | JSON 結構描述檔案的位置，說明範本語言的版本。您應該使用上述的 URL。
| contentVersion | 是 | 範本版本 (例如 1.0.0.0)。您可以為此元素提供任何值。使用範本部署資源時，這個值可用來確定使用的是正確的範本。
| 參數 | 否 | 執行部署以自訂資源部署時所提供的值。
| 變數 | 否 | 範本中做為 JSON 片段以簡化範本語言運算式的值。
| 資源 | 是 | 在資源群組中部署或更新的資源類型。
| 輸出 | 否 | 部署後傳回的值。

本主題稍後處將會檢視範本的區段。現在，我們將檢視組成範本的一些語法。

## 運算式和函式

範本的基本語法是 JSON，但運算式與函式能夠延伸範本中的 JSON，讓您能夠建立不是嚴格常數值的值。運算式以方括弧 [ 及 ] 括住，並會在部署範本後評估。運算式可出現在 JSON 字串值中的任何一處，並一律傳回另一個 JSON 值。如果您必須使用開頭為括號 [ 的常數字串，您必須使用兩個括號 [[。

一般而言，您可以將運算式搭配函數使用，以執行可設定部署的作業。正如同在 JavaScript 中，函數呼叫的格式為 **functionName(arg1,arg2,arg3)**。您可以使用點與 [index] 運算子來參考屬性。

下列範例將示範如何在結構化值時使用數個函數：
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

如需範本函數的完整清單，請參閱 [Azure 資源管理員範本函數](resource-group-template-functions.md)。


## 參數

在範本的 parameters 區段中，您會指定可在部署資源時輸入的值。提供針對特定環境 (例如開發、測試和生產環境) 量身訂做的參數值，可讓您自訂部署。您不必在範本中提供參數，但若沒有參數，您的範本一律會部署具有相同名稱、位置和屬性的相同資源。

您可以在整個範本中使用這些參數值，為所部署的資源設定值。只有在參數區段中宣告的參數可以用於範本的其他區段中。

您會定義結構如下的參數：

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| 元素名稱 | 必要 | 說明
| :------------: | :------: | :----------
| parameterName | 是 | 參數名稱。必須是有效的 JavaScript 識別碼。
| 類型 | 是 | 參數值類型。請參閱下列允許類型清單。
| defaultValue | 否 | 如果未提供參數值，則會使用參數的預設值。
| allowedValues | 否 | 參數的允許值陣列，確保提供正確的值。
| minValue | 否 | int 類型參數的最小值，含此值。
| maxValue | 否 | int 類型參數的最大值，含此值。
| minLength | 否 | 字串、secureString 及陣列類型參數長度的最小值，含此值。
| maxLength | 否 | 字串、secureString 及陣列類型參數長度的最大值，含此值。
| 說明 | 否 | 經由入口網站自訂範本介面顯示給範本使用者的參數說明。

允許的類型和值為：

- string 或 secureString - 任何有效的 JSON 字串
- int - 任何有效的 JSON integer
- bool - 任何有效的 JSON 布林值
- object 或 secureObject - 任何有效的 JSON 物件
- array - 任何有效的 JSON 陣列

若要將參數指定為選用，請提供 defaultValue (可為空字串)。

如果您指定的參數名稱與範本部署命令中的某個參數相同 (例如範本中包含名為 **ResourceGroupName** 的參數，而該名稱與 [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) Cmdlet 中的 **ResourceGroupName** 參數相同)，則系統會提示您為後置詞是 **FromTemplate** 的參數 (例如 **ResourceGroupNameFromTemplate**) 提供一個值。一般而言，在為參數命名時，請勿使用與部署作業所用參數相同的名稱，以避免發生這種混淆的情形。

>[AZURE.NOTE] 所有密碼、金鑰和其他密碼都應該使用 **secureString** 類型。部署資源後，無法讀取類型為 secureString 的範本參數。

下列範例示範如何定義參數：

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

如需如何在部署期間輸入參數值的資訊，請參閱[使用 Azure Resource Manager 範本部署資源](resource-group-template-deploy.md#parameter-file)。

## 變數

在 variables 區段中，您會建構可用於整個範本中的值。一般而言，這些變數將會以參數所提供的值為根據。您不需要定義變數，但它們通常會經由減少複雜運算式來簡化您的範本。

您可使用以下結構定義變數：

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

下列範例說明如何定義由兩個參數值建構的變數：

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

接下來的範例說明複雜 JSON 類型的變數，以及由其他變數建構的變數：

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## 資源

在資源區段中，您會定義要部署或更新資源。這是您的範本可以變得更複雜的地方，因為您必須了解您要部署的類型才能提供正確的值。如果要進一步了解資源提供者，請參閱 [Resource Manager 提供者、區域、API 版本及結構描述](resource-manager-supported-services.md)。

您會定義結構如下的資源：

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| 元素名稱 | 必要 | 說明
| :----------------------: | :------: | :----------
| apiVersion | 是 | 要用來建立資源的 REST API 版本。如要為特定資源類型判斷可用的版本號碼，請參閱[支援的 API 版本](resource-manager-supported-services.md#supported-api-versions)。
| 類型 | 是 | 資源類型。這個值是資源提供者的命名空間與資源提供者所支援資源類型的組合。
| 名稱 | 是 | 資源名稱。此名稱必須遵循在 RFC3986 中定義的 URI 元件限制。此外，將資源名稱公開到外部合作對象的 Azure 服務會驗證該名稱，確定不是有人嘗試詐騙其他身分識別。請參閱[請檢查資源名稱](https://msdn.microsoft.com/library/azure/mt219035.aspx)。
| location | 視情況而異 | 所提供資源的支援地理位置。若要判斷可用的位置，請參閱[支援的區域](resource-manager-supported-services.md#supported-regions)。大部分的資源類型都需要有位置，但某些類型 (例如角色指派) 不需要位置。
| tags | 否 | 與資源相關聯的標記。
| 註解 | 否 | 您在範本中記錄資源的註解
| dependsOn | 否 | 正在定義的資源所相依的資源。評估資源與依相依順序部署資源之間的相依性。資源若不互相依賴，則會嘗試平行部署資源。值可以是以逗號分隔的資源名稱或資源唯一識別碼清單。
| 屬性 | 否 | 資源特定的組態設定。properties 的值和您在 REST API 作業 (PUT 方法) 要求主體中提供來建立資源的值是完全一樣的。如需資源結構描述文件或 REST API 的連結，請參閱[資訊管理員提供者、區域、API 版本及結構描述](resource-manager-supported-services.md)。
| 資源 | 否 | 與正在定義的資源相依的下層資源。您只能提供父資源的結構描述所允許的資源類型。子資源類型的完整名稱包含父資源的名稱，例如 **Microsoft.Web/sites/extensions**。父資源的相依性不是隱含的；您必須明確定義該相依性。 


如果資源名稱不是唯一，您可以使用 **resourceId** Helper 函式 (如下所述) 來取得任何資源的唯一識別碼。

resources 區段包含要部署的資源陣列。在每個資源內，您也可以定義該資源的子資源陣列。因此，您的 resources 區段可能會有類似以下的結構：

    "resources": [
       {
           "name": "resourceA",
           ...
       },
       {
           "name": "resourceB",
           ...
           "resources": [
               {
                   "name": "firstChildResourceB",
                   ...
               },
               {   
                   "name": "secondChildResourceB",
                   ...
               }
           ]
       },
       {
           "name": "resourceC",
           ...
       }
    ]



下列範例顯示 **Microsoft.Web/serverfarms** 資源，以及含子系 **Extensions** 資源的 **Microsoft.Web/sites** 資源。請注意，網站會標示為依存於伺服器陣列，因為伺服器陣列必須存在，才能部署網站。也請注意，**Extensions** 資源是網站的子系。

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "ARM"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## 輸出

在輸出區段中，您可以指定從部署傳回的值。例如，您可以傳回 URI 以存取所部署的資源。

下列範例顯示輸出定義的結構：

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| 元素名稱 | 必要 | 說明
| :------------: | :------: | :----------
| outputName | 是 | 輸出值的名稱。必須是有效的 JavaScript 識別碼。
| 類型 | 是 | 輸出值的類型。輸出值支援與範本輸入參數相同的類型。
| value | 是 | 將進行評估並傳回做為輸出值的範本語言運算式。


下列範例顯示在輸出區段中傳回的值。

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

如需使用輸出的相關資訊，請參閱[Azure Resource Manager 範本中的共用狀態](best-practices-resource-manager-state.md)。

## 後續步驟
- 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
- 如需您可以在範本內使用哪些函式的詳細資料，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
- 若要在部署期間合併多個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
- 若要依指定的次數重複建立資源類型，請參閱[在 Azure 資源管理員中建立資源的多個執行個體](resource-group-create-multiple.md)。
- 您可能需要使用不同資源群組內的資源。這常見於使用多個資源群組之間所共用的儲存體帳戶或虛擬網路時。如需詳細資訊，請參閱 [resourceId 函式](resource-group-template-functions.md#resourceid)。

<!---HONumber=AcomDC_0706_2016-->