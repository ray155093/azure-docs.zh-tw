---
title: "建立 Azure 部署的範本 | Microsoft Docs"
description: "使用宣告式 JSON 語法描述 Azure Resource Manager 範本的結構和屬性。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a75863878a97c1202e9b9946b0bff19261952c21
ms.lasthandoff: 03/22/2017


---
# <a name="authoring-azure-resource-manager-templates"></a>編寫 Azure 資源管理員範本
本主題說明 Azure Resource Manager 範本的結構。 它會呈現範本的不同區段，以及這些區段中可用的屬性。 範本由 JSON 與運算式所組成，可讓您用來為部署建構值。 

若要檢視您已部署的資源範本，請參閱[從現有資源匯出 Azure Resource Manager 範本](resource-manager-export-template.md)。 如需建立範本的指導方針，請參閱 [Resource Manager 範本逐步解說](resource-manager-template-walkthrough.md)。 如需建立範本的建議，請參閱 [建立 Azure Resource Manager 範本的最佳做法](resource-manager-template-best-practices.md)。

良好的 JSON 編輯器可簡化範本建立工作。 如需如何以您的範本使用 Visual Studio 的相關資訊，請參閱 [透過 Visual Studio 建立和部署 Azure 資源群組](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。 如需如何使用 VS Code 的相關資訊，請參閱 [在 Visual Studio Code 中使用 Azure Resource Manager 範本](resource-manager-vs-code.md)。

將範本大小限制為 1 MB，並將每個參數檔案限制為 64 KB。 1 MB 的限制適用於已增加反覆資源定義和變數和參數值之範本的最終狀態。 

## <a name="template-format"></a>範本格式
在最簡單的結構中，範本包含下列元素：

```json
{
   "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "",
   "parameters": {  },
   "variables": {  },
   "resources": [  ],
   "outputs": {  }
}
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| $schema |是 |JSON 結構描述檔案的位置，說明範本語言的版本。 使用上述範例所示的 URL。 |
| contentVersion |是 |範本版本 (例如 1.0.0.0)。 您可以為此元素提供任何值。 使用範本部署資源時，這個值可用來確定使用的是正確的範本。 |
| 參數 |否 |執行部署以自訂資源部署時所提供的值。 |
| 變數 |否 |範本中做為 JSON 片段以簡化範本語言運算式的值。 |
| 資源 |是 |在資源群組中部署或更新的資源類型。 |
| 輸出 |否 |部署後傳回的值。 |

我們在本主題稍後更詳細探討範本的各個區段。

## <a name="expressions-and-functions"></a>運算式和函式
範本的基本語法是 JSON。 不過，運算式和函式可延伸範本中提供的 JSON 值。  運算式是在 JSON 字串常值內撰寫，其第一個字元和最後一個字元分別為下列兩種方括號：`[` 和 `]`。 部署範本時，會評估運算式的值。   雖然是以字串常值撰寫，評估運算式的結果，根據實際的運算式可能會是不同的 JSON 類型 (例如陣列或整數)。  請注意，針對開頭為方括號 `[` 的常值字串，若不要將它解譯為運算式，請加入額外的方括號，使字串開頭成為 `[[`。

一般而言，您可以將運算式搭配函數使用，以執行可設定部署的作業。 正如同在 JavaScript 中，函數呼叫的格式為 **functionName(arg1,arg2,arg3)**。 您可以使用點與 [index] 運算子來參考屬性。

下列範例將示範如何在結構化值時使用數個函數：

```json
"variables": {
   "location": "[resourceGroup().location]",
   "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
   "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

如需範本函數的完整清單，請參閱 [Azure 資源管理員範本函數](resource-group-template-functions.md)。 

## <a name="parameters"></a>參數
在範本的 parameters 區段中，您會指定可在部署資源時輸入的值。 提供針對特定環境 (例如開發、測試和生產環境) 量身訂做的參數值，可讓您自訂部署。 您不必在範本中提供參數，但若沒有參數，您的範本一律會部署具有相同名稱、位置和屬性的相同資源。

您可以在整個範本中使用這些參數值，為所部署的資源設定值。 只有在參數區段中宣告的參數可以用於範本的其他區段中。

您會定義結構如下的參數：

```json
"parameters": {
   "<parameter-name>" : {
     "type" : "<type-of-parameter-value>",
     "defaultValue": "<default-value-of-parameter>",
     "allowedValues": [ "<array-of-allowed-values>" ],
     "minValue": <minimum-value-for-int>,
     "maxValue": <maximum-value-for-int>,
     "minLength": <minimum-length-for-string-or-array>,
     "maxLength": <maximum-length-for-string-or-array-parameters>,
     "metadata": {
         "description": "<description-of-the parameter>" 
     }
   }
}
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| parameterName |是 |參數名稱。 必須是有效的 JavaScript 識別碼。 |
| 類型 |是 |參數值類型。 請參閱此表格之後的允許類型清單。 |
| defaultValue |否 |如果未提供參數值，則會使用參數的預設值。 |
| allowedValues |否 |參數的允許值陣列，確保提供正確的值。 |
| minValue |否 |int 類型參數的最小值，含此值。 |
| maxValue |否 |int 類型參數的最大值，含此值。 |
| minLength |否 |字串、secureString 及陣列類型參數長度的最小值，含此值。 |
| maxLength |否 |字串、secureString 及陣列類型參數長度的最大值，含此值。 |
| 說明 |否 |經由入口網站自訂範本介面顯示給範本使用者的參數說明。 |

允許的類型和值為：

* **字串**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

若要將參數指定為選用，請提供 defaultValue (可為空字串)。 

如果您在範本中指定的參數名稱和要部署範本的命令中的參數相符，提供的值就有可能模稜兩可。 Resource Manager 會在範本參數加上後置詞 **FromTemplate** 以避免混淆。 例如，如果您在範本中包含名為 **ResourceGroupName** 的參數，這會與 [New-AzureRmResourceGroupDeployment][deployment2cmdlet] Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 部署期間，系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。 一般而言，在為參數命名時，請勿使用與部署作業所用參數相同的名稱，以避免發生這種混淆的情形。

> [!NOTE]
> 所有密碼、金鑰和其他密碼都應該使用 **secureString** 類型。 如果您在 JSON 物件中傳遞敏感資料，請使用 **secureObject** 類型。 部署資源後，無法讀取類型為 secureString 或 secureObject 的範本參數。 
> 
> 例如，在部署歷程記錄中，下列項目會顯示字串和物件的值，但不會顯示 secureString 和 secureObject 的值。
>
> ![show deployment values](./media/resource-group-authoring-templates/show-parameters.png)  
>

下列範例示範如何定義參數：

```json
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
```

如需如何在部署期間輸入參數值的資訊，請參閱 [使用 Azure Resource Manager 範本部署資源](resource-group-template-deploy.md)。 

## <a name="variables"></a>變數
在 variables 區段中，您會建構可用於整個範本中的值。 一般而言，變數會以參數所提供的值為根據。 您不需要定義變數，但它們通常會經由減少複雜運算式來簡化您的範本。

您可使用以下結構定義變數：

```json
"variables": {
   "<variable-name>": "<variable-value>",
   "<variable-name>": { 
       <variable-complex-type-value> 
   }
}
```

下列範例說明如何定義由兩個參數值建構的變數：

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

接下來的範例說明複雜 JSON 類型的變數，以及由其他變數建構的變數：

```json
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
```

## <a name="resources"></a>資源
在資源區段中，您會定義要部署或更新的資源。 此區段會變得複雜，因為您必須了解您要部署的類型才能提供正確的值。 

您會定義結構如下的資源：

```json
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
     "copy": {
       "name": "<name-of-copy-loop>",
       "count": "<number-of-iterations>"
     },
     "resources": [
       "<array-of-child-resources>"
     ]
   }
]
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| apiVersion |是 |要用來建立資源的 REST API 版本。 |
| 類型 |是 |資源類型。 這個值是資源提供者的命名空間與資源類型的組合 (例如 **Microsoft.Storage/storageAccounts**)。 |
| 名稱 |是 |資源名稱。 此名稱必須遵循在 RFC3986 中定義的 URI 元件限制。 此外，將資源名稱公開到外部合作對象的 Azure 服務會驗證該名稱，確定不是有人嘗試詐騙其他身分識別。 請參閱 [請檢查資源名稱](https://msdn.microsoft.com/library/azure/mt219035.aspx)。 |
| location |視情況而異 |所提供資源的支援地理位置。 您可以選取任何可用的位置，但通常選擇接近您的使用者的位置很合理。 通常，將彼此互動的資源放在相同區域也合乎常理。 大部分的資源類型都需要有位置，但某些類型 (例如角色指派) 不需要位置。 |
| tags |否 |與資源相關聯的標記。 |
| 註解 |否 |您在範本中記錄資源的註解 |
| dependsOn |否 |在部署這項資源之前必須部署的資源。 Resource Manager 會評估資源之間的相依性，並依正確的順序進行部署。 資源若不互相依賴，則會平行部署資源。 值可以是以逗號分隔的資源名稱或資源唯一識別碼清單。 只會列出此範本中已部署的資源。 此範本中未定義的資源必須已經存在。 避免加入不必要的相依性，因為可能會降低部署速度並產生循環相依性。 如需設定相依性的指引，請參閱[定義 Azure Resource Manager 範本中的相依性](resource-group-define-dependencies.md)。 |
| 屬性 |否 |資源特定的組態設定。 屬性的值和您在 REST API 作業 (PUT 方法) 要求主體中提供來建立資源的值是一樣的。 如需資源結構描述文件或 REST API 的連結，請參閱 [Resource Manager 提供者、區域、API 版本及結構描述](resource-manager-supported-services.md)。 |
| 複製 |否 |如果需要多個執行個體，要建立的資源數目。 如需詳細資訊，請參閱[在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)。 |
| 資源 |否 |與正在定義的資源相依的下層資源。 只提供父資源的結構描述允許的資源類型。 子資源的完整類型包含父資源類型，例如 **Microsoft.Web/sites/extensions**。 沒有隱含父資源的相依性。 您必須明確定義該相依性。 |

了解要為 **apiVersion**、**type** 和 **location** 指定的值目前不明顯。 幸運的是，您可以透過 Azure PowerShell 或 Azure CLI 判斷這些值。

若要使用 **PowerShell** 取得所有資源提供者，請使用：

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

從傳回的清單，尋找您感興趣的資源提供者。 若要取得資源提供者 (例如儲存體) 的資源類型，請使用：

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes
```

若要取得資源類型 (這類儲存體帳戶) 的 API 版本，請使用：

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions
```

若要取得資源類型支援的位置，請使用：

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations
```

若要使用 **Azure CLI** 取得所有資源提供者，請使用：

```azurecli
azure provider list
```

從傳回的清單，尋找您感興趣的資源提供者。 若要取得資源提供者 (例如儲存體) 的資源類型，請使用：

```azurecli
azure provider show Microsoft.Storage
```

若要取得支援的位置和 API 版本，請使用：

```azurecli
azure provider show Microsoft.Storage --details --json
```

若要進一步了解資源提供者，請參閱 [Resource Manager 提供者、區域、API 版本及結構描述](resource-manager-supported-services.md)。

resources 區段包含要部署的資源陣列。 在每個資源內，您也可以定義子資源陣列。 因此，您的 resources 區段可能會有類似以下的結構：

```json
"resources": [
   {
       "name": "resourceA",
   },
   {
       "name": "resourceB",
       "resources": [
           {
               "name": "firstChildResourceB",
           },
           {   
               "name": "secondChildResourceB",
           }
       ]
   },
   {
       "name": "resourceC",
   }
]
```      

下列範例顯示 **Microsoft.Web/serverfarms** 資源，以及含子系 **Extensions** 資源的 **Microsoft.Web/sites** 資源。 請注意，網站會標示為依存於伺服器陣列，因為伺服器陣列必須存在，才能部署網站。 也請注意， **Extensions** 資源是網站的子系。

```json
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
      "team": "Web"
    },
    "dependsOn": [
      "[concat(parameters('hostingPlanName'))]"
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
```

## <a name="outputs"></a>輸出
在輸出區段中，您可以指定從部署傳回的值。 例如，您可以傳回 URI 以存取所部署的資源。

下列範例顯示輸出定義的結構：

```json
"outputs": {
   "<outputName>" : {
     "type" : "<type-of-output-value>",
     "value": "<output-value-expression>"
   }
}
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| outputName |是 |輸出值的名稱。 必須是有效的 JavaScript 識別碼。 |
| 類型 |是 |輸出值的類型。 輸出值支援與範本輸入參數相同的類型。 |
| value |是 |評估並傳回做為輸出值的範本語言運算式。 |

下列範例顯示在輸出區段中傳回的值。

```json
"outputs": {
   "siteUri" : {
     "type" : "string",
     "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
   }
}
```

如需使用輸出的相關資訊，請參閱 [Azure Resource Manager 範本中的共用狀態](best-practices-resource-manager-state.md)。

## <a name="next-steps"></a>後續步驟
* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
* 如需您可以在範本內使用哪些函式的詳細資料，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
* 若要在部署期間合併多個範本，請參閱 [透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
* 您可能需要使用不同資源群組內的資源。 這案例常見於使用多個資源群組之間所共用的儲存體帳戶或虛擬網路時。 如需詳細資訊，請參閱 [resourceId 函式](resource-group-template-functions.md#resourceid)。

[deployment2cmdlet]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment

