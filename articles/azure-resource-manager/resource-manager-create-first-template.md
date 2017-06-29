---
title: "建立第一個 Azure Resource Manager 範本 | Microsoft Docs"
description: "說明如何建立第一個 Azure Resource Manager 範本的逐步指南。 它說明如何使用儲存體帳戶的範本參考來建立範本。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/18/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 80fd9d79652e4f0d9c4c524e3a762bcc3462bb53
ms.contentlocale: zh-tw
ms.lasthandoff: 06/02/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>建立第一個 Azure Resource Manager 範本
本主題會逐步引導您完成建立第一個 Azure Resource Manager 範本的步驟。 Resource Manager 範本是 JSON 檔案，該檔案定義您需要為您的解決方案部署的資源。 若要了解部署和管理 Azure 解決方案的相關概念，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。 如果您有現成的資源且想要取得這些資源的範本，請參閱[從現有資源匯出 Azure Resource Manager 範本](resource-manager-export-template.md)。

若要建立並修改範本，您需要 JSON 編輯器。 [Visual Studio Code](https://code.visualstudio.com/) 是輕量型、開放原始碼、跨平台的程式碼編輯器。 它會透過擴充功能，支援建立和編輯 Resource Manager 範本。 本主題假設您使用 VS Code；不過，如果您有其他 JSON 編輯器 (如 Visual Studio)，您可以使用該編輯器。

## <a name="get-vs-code-and-extension"></a>取得 VS Code 和擴充功能
1. 如有需要，請從 [https://code.visualstudio.com/](https://code.visualstudio.com/) 安裝 VS Code。

2. 存取快速開啟 (Ctrl+P) 並執行，以安裝 [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) 擴充功能︰ 

   ```
   ext install msazurermtools.azurerm-vscode-tools
   ```

3. 在系統提示時重新啟動 VS Code 以啟用擴充功能。

## <a name="create-blank-template"></a>建立空白範本

讓我們從只包含範本基本區段的空白範本著手。

1. 建立檔案。 

2. 複製以下 JSON 語法並貼到您的檔案中：

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [  ],
     "outputs": {  }
   }
   ```

3. 將此檔案儲存為 azuredeploy.json。 

## <a name="add-storage-account"></a>新增儲存體帳戶
1. 若要定義部署的儲存體帳戶，您可將該儲存體帳戶新增至範本的 **resources** 區段。 若要尋找儲存體帳戶可用的值，請查看[儲存體帳戶範本參考](/azure/templates/microsoft.storage/storageaccounts)。 複製針對儲存體帳戶顯示的 JSON。 

3. 將該 JSON 貼到範本 **resources** 區段，如下列範例所示︰ 

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [
       {
         "name": "string",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-12-01",
         "sku": {
           "name": "string"
         },
         "kind": "string",
         "location": "string",
         "tags": {},
         "properties": {
           "customDomain": {
             "name": "string",
             "useSubDomain": boolean
           },
           "encryption": {
             "services": {
               "blob": {
                 "enabled": boolean
               }
             },
             "keySource": "Microsoft.Storage"
           },
           "accessTier": "string"
         }
       }
     ],
     "outputs": {  }
   }
   ```

  VS Code 可能會表示 2016-12-01 不是有效的 API 版本。 如果您是從範本參考文件使用版本號碼，可以忽略此警告。 尚未使用來自資源提供者的最新版本號碼更新結構描述時，您就會看到這個警告。 
  
  上述範例包含許多預留位置值，以及您的儲存體帳戶可能不需要的一些屬性。

## <a name="set-values-for-storage-account"></a>設定儲存體帳戶的值

現在，您已經準備好設定儲存體帳戶的值。 

1. 再次查看您複製 JSON 的[儲存體帳戶範本參考](/azure/templates/microsoft.storage/storageaccounts)。 有數個說明屬性並提供可用值的表格。 

2. 請注意，在 **properties** 元素中，**customDomain**、**encryption** 和 **accessTier** 全都列為非必要。 這些值對您的案例而言可能很重要，但是為了方便說明此範例，讓我們將它們移除。

   ```json
   "resources": [
     {
       "name": "string",
       "type": "Microsoft.Storage/storageAccounts",
       "apiVersion": "2016-12-01",
       "sku": {
         "name": "string"
       },
       "kind": "string",
       "location": "string",
       "tags": {},
       "properties": {
       }
     }
   ],
   ```

3. **kind** 元素目前設定為預留位置值 ("string")。 VS Code 包含許多功能，可協助您了解要使用於範本中的值。 請注意，VS Code 指出此值無效。 如果您將滑鼠移至 "string" 上，VS Code 會建議 **kind** 的有效值為 `Storage` 或 `BlobStorage`。 

   ![顯示 VS Code 建議值](./media/resource-manager-create-first-template/vs-code-show-values.png)

   若要查看可用的值，刪除雙引號之間的字元並選取 **Ctrl+空格鍵**。 從可用的選項中選取 [儲存體]。
  
   ![顯示 intellisense](./media/resource-manager-create-first-template/intellisense.png)

   如果您未使用 VS Code，請查看儲存體帳戶範本參考頁面。 請注意，說明會列出相同的有效值。 將元素設定為 **Storage**。

   ```json
   "kind": "Storage",
   ```

您的範本現在如下所示：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>新增範本函式

您可在範本內使用函式，以簡化範本的語法，以及擷取只有在部署範本時可以使用的值。 如需完整的範本函式集，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。

若要指定將儲存體帳戶部署至與資源群組相同的位置，請將 **location** 屬性設定為︰

```json
"location": "[resourceGroup().location]",
```

同樣地，VS Code 可藉由建議可用的函式來協助您。 

![顯示函式](./media/resource-manager-create-first-template/show-functions.png)

請注意，函式是以方括號括住。 [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) 函式會傳回具有 `location` 屬性的物件。 資源群組會保留您解決方案的所有相關資源。 您可以將 location 屬性硬式編碼為 "Central US" 之類的值，但您必須手動變更範本以重新部署到不同的位置。 使用 `resourceGroup` 函式，輕鬆地將此範本重新部署至不同位置的不同資源群組。

您的範本現在如下所示：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-parameters-and-variables"></a>新增參數和變數
您的範本只剩下兩個值要設定 - **name** 和 **sku.name**。 對於這些屬性，您可新增可讓您在部署期間自訂這些值的參數。 

儲存體帳戶名稱有幾項限制，以致它們難以設定。 名稱的長度必須介於 3 到 24 個字元，只能使用數字和小寫字母，而且必須是唯一的。 請使用 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函式來產生雜湊值，而非嘗試猜測符合限制的唯一值。 若要讓此雜湊值更具意義，請加上前置詞，協助您在部署後將它識別為儲存體帳戶。 

1. 若要傳入符合您的命名慣例的名稱前置詞，請移至範本的 **parameters** 區段。 將參數新增至可接受儲存體帳戶名稱前置詞的範本︰

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     }
   },
   ```

  前置詞受限於最多 11 個字元，因為 `uniqueString` 會傳回 13 個字元，而且名稱不能超過 24 個字元。 如果您未在部署期間傳入此參數的值，則會使用預設值。

2. 移至範本的 **variables** 區段。 若要從前置詞和唯一字串建構名稱，請新增下列變數︰

   ```json
   "variables": {
     "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
   },
   ```

3. 在 **resources** 區段中，將儲存體帳戶名稱設定為該變數。

   ```json
   "name": "[variables('storageName')]",
   ```

3. 若要能夠針對儲存體帳戶傳入不同的 SKU，請移至 **parameters** 區段。 在儲存體名稱前置詞的參數之後，新增一個參數，以指定允許的 SKU 值和預設值。 您可以從範本參考頁面或 VS Code 中找到允許的值。 在下列範例中，您會包含 SKU 的所有有效值。 不過，您可將允許的值僅限於您要透過此範本部署的 SKU 類型。

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     },
     "storageSKU": {
       "type": "string",
       "allowedValues": [
         "Standard_LRS",
         "Standard_ZRS",
         "Standard_GRS",
         "Standard_RAGRS",
         "Premium_LRS"
       ],
       "defaultValue": "Standard_LRS",
       "metadata": {
         "description": "The type of replication to use for the storage account."
       }
     }
   },
   ```

3. 將 SKU 屬性變更為使用來自參數的值︰

   ```json
   "sku": {
     "name": "[parameters('storageSKU')]"
   },
   ```    

4. 儲存您的檔案。

## <a name="final-template"></a>最終範本

完成本文中的步驟之後，您的範本現在如下所示︰

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>後續步驟
* 您的範本已完成，您可準備將它部署到您的訂用帳戶。 若要部署，請參閱[將資源部署至 Azure](resource-manager-quickstart-deploy.md)。
* 若要深入了解範本的結構，請參閱 [編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。

