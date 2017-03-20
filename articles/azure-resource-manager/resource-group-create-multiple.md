---
title: "部署 Azure 資源的多個執行個體 | Microsoft Docs"
description: "使用「Azure 資源管理員」範本中的複製作業和陣列，並在部署資源時多次逐一執行。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 04a3866f88b00486c30c578699d34cd6e8e776d7
ms.openlocfilehash: 056ee5e67b9a6d396586c53b04d50f89e6fbb560
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>在 Azure Resource Manager 範本中部署資源的多個執行個體
此主題說明如何逐一查看您的「Azure 資源管理員」範本，以建立資源的多個執行個體。

## <a name="copy-copyindex-and-length"></a>copy、copyIndex 和 length
若要在資源中建立多次，您可以定義 **copy** 物件，以指定逐一執行的次數。 copy 的格式如下：

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

您可以使用 **copyIndex()** 函式存取目前的反覆運算值。 下列範例搭配使用 copyIndex 與 concat 函式來建構名稱。

```json
[concat('examplecopy-', copyIndex())]
```

從值的陣列建立多個資源時，您可以使用 **length** 函數來指定計數。 您可以提供陣列為 length 函數的參數。

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

您只能將 copy 物件套用至最上層資源。 您不能將它套用至資源類型上的屬性或套用至子資源。 不過，本主題會說明如何為屬性指定多個項目，並為子資源建立多個執行個體。 下列虛擬程式碼範例示範可將 copy 套用到的位置︰

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* no, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* copy can be applied if resource is promoted to top level */ 
      }
    ]
  }
] 
```

雖然您無法將 **copy** 套用到屬性，該屬性仍屬於屬性所在資源的反覆運算。 因此，您可以在屬性內使用 **copyIndex()** 來指定值。

您可能會在數種情況下，想要對資源中的屬性執行反覆運算。 例如，您可能想要對虛擬機器指定多個資料磁碟。 若要了解如何對屬性執行反覆運算，請參閱[在複製無法使用時建立多個執行個體](#create-multiple-instances-when-copy-wont-work)。 

若要使用子資源，請參閱[為子資源建立多個執行個體](#create-multiple-instances-of-a-child-resource)。

## <a name="use-index-value-in-name"></a>在名稱中使用索引值
您可以使用 copy 作業建立資源的多個執行個體，並以遞增的索引值命名其唯一名稱。 例如，您可以在每個已部署之資源名稱的結尾加上唯一的數字。 部署名稱如下的三個網站：

* examplecopy-0
* examplecopy-1
* examplecopy-2。

使用下列範本：

```json
"parameters": { 
  "count": { 
    "type": "int", 
    "defaultValue": 3 
  } 
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[parameters('count')]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      }
  } 
]
```

## <a name="offset-index-value"></a>位移索引值
在上述範例中，索引值會從 0 到 2。 若要位移索引值，您可以傳遞 **copyIndex()** 函數中的值，例如 **copyIndex(1)**。 要執行的反覆項目數仍然在複製項目中指定，但 copyIndex 的值會由指定的值位移。 因此，使用和前一個範例相同的範本，但指定 **copyIndex(1)** 會部署下列三個名稱的網站：

* examplecopy-1
* examplecopy-2
* examplecopy-3

## <a name="use-copy-with-array"></a>搭配陣列使用複製
使用陣列時，複製作業會有幫助，因為您可以逐一查看陣列中的每個項目。 部署名稱如下的三個網站：

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

使用下列範本：

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "Contoso", 
         "Fabrikam", 
         "Coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

您當然可以將列印張數設為陣列長度以外的值。 比方說，您可以建立具有許多值的陣列，然後傳入參數值，指定要部署多少陣列項目。 在此情況下，您可以設定列印張數，如第一個範例所示。 

## <a name="depend-on-resources-in-a-loop"></a>依迴圈中的資源而定
您可以透過使用 **dependsOn** 項目來指定在另一個資源之後部署資源。 若要部署相依於迴圈中之資源集合的資源時，請在 **dependsOn** 項目中提供複製迴圈的名稱。 下列範例示範如何在部署虛擬機器之前部署三個儲存體帳戶。 不會顯示完整的虛擬機器定義。 請注意，copy 項目將**名稱**設定為 **storagecopy** 且虛擬機器的 **dependsOn** 項目也設定為 **storagecopy**。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2015-06-15",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
            "location": "[resourceGroup().location]",
            "properties": {
                "accountType": "Standard_LRS"
            },
            "copy": { 
                "name": "storagecopy", 
                "count": 3 
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>為子資源建立多個執行個體
您無法為子資源使用複製迴圈。 若要為通常定義為巢狀在另一個資源內的資源建立多個執行個體，您必須改為將該資源建立為最上層資源。 您可以透過 **type** 和 **name** 屬性定義與父資源的關聯性。

例如，假設您通常將資料集定義為 Data Factory 中的子資源。

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

若要為資料集建立多個執行個體，請在其移至 Data Factory 外。 資料集必須位於和 Data Factory 相同的層級，但它仍是 Data Factory 的子資源。 您可以透過 **type** 和 **name** 屬性，保留資料集和 Data Factory 之間的關聯性。 由於無法再從類型位於範本中的位置來推斷類型，您必須以此格式提供完整的類型︰`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`。

若要建立與 Data Factory 執行個體的父/子關聯性，請提供包含父資源名稱之資料集的名稱。 使用格式︰`{parent-resource-name}/{child-resource-name}`。  

下列範例顯示實作：

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="create-multiple-instances-when-copy-wont-work"></a>在複製無法使用時建立多個執行個體
您只能在資源類型上使用 **copy** ，不能在資源類型內的屬性上使用。 當您想要建立某些項目的多個執行個體，而這些項目屬於資源一部分時，此需求可能會產生問題。 常見的案例是針對一部虛擬機器建立多個資料磁碟。 您不能將 **copy** 與資料磁碟搭配使用，因為 **dataDisks** 是虛擬機器上的屬性，而不是它自己的資源類型。 您要改為建立具有所需磁碟陣列數目的陣列，然後傳入實際要建立的資料磁碟數量。 在虛擬機器定義中，使用 **take** 函式從陣列中取得實際需要的元素數目。

此模式的完整範例會顯示於 [Create a VM with a dynamic selection of data disks (使用動態選取的資料磁碟來建立 VM)](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) 範本中。

下列範例會顯示部署範本的相關區段。 已移除範本中的大部分內容，以便醒目顯示動態建立多個資料磁碟時所涉及的區段。 請注意參數 **numDataDisks** ，其可讓您傳入要建立的磁碟數目。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

當您必須為屬性項目數變化不定的資源建立多個執行個體時，您可以使用 **take** 函式和 **copy** 項目。 例如，假設您需要建立多個虛擬機器，但每個虛擬機器的資料磁碟數不同。 為了對每個資料磁碟賦予名稱以便識別相關聯的虛擬機器，請將您的資料磁碟陣列放在另一個範本。 納入虛擬機器名稱的參數，以及要傳回的資料磁碟數目。 在 [輸出] 區段中，傳回指定項目的數目。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

在父範本中，您要納入虛擬機器數目的參數，以及每個虛擬機器之資料磁碟數目的陣列。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

在 [資源] 區段中，為定義資料磁碟的範本部署多個執行個體。 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

在 [資源] 區段中，為虛擬機器部署多個執行個體。 對於資料磁碟，參考包含正確資料磁碟數和正確資料磁碟名稱的巢狀部署。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="return-values-from-a-loop"></a>從迴圈傳回值
雖然您可以方便地為資源類型建立多個執行個體，但從該迴圈傳回值卻很困難。 其中一種保留並傳回值的方式是對巢狀範本使用 **copy**，並對包含所有欲傳回值的陣列執行來回傳送作業。 例如，假設您想要建立多個儲存體帳戶，並傳回其各自的主要端點。 

首先，請建立可建立儲存體帳戶的巢狀範本。 請注意，它會接受 Blob URI 的陣列參數。 您可以使用這個參數，以從先前的部署來回傳送所有值。 範本的輸出是會將新的 Blob URI 串連到先前 URI 的陣列。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "indexValue": {
      "type":"int"
    },
    "blobURIs": {
        "type": "array",
      "defaultValue": []
    }
  },
    "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  },
    "resources": [
    {
        "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {  
      }
    }
    ],
    "outputs": {
      "result": {
        "type": "array",
      "value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    }
  }
}
```

現在，建立父範本，使其具有一個靜態的巢狀範本執行個體，並循環建立巢狀範本的剩餘執行個體。 針對迴圈部署的每個執行個體，請傳遞身為先前部署之輸出的陣列。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberofStorage": { "type": "int", "minValue": 2 }
  },
  "resources": [
    {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate0",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": 0}
        }
      }
    },
    {
      "apiVersion": "2016-09-01",
      "name": "[concat('nestedTemplate', copyIndex(1))]",
      "type": "Microsoft.Resources/deployments",
      "copy": {
        "name": "storagecopy",
        "count": "[sub(parameters('numberofStorage'), 1)]"
      },
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": "[copyIndex(1)]"},
          "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟
* 若要了解範本區段的相關資訊，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 如需可以在範本中使用的函式清單，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
* 若要了解如何部署範本，請參閱 [使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。


