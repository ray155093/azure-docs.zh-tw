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
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8ecf7c058b90fd18e41fd4e1cbc29e22dfeb0883
ms.lasthandoff: 04/18/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>在 Azure Resource Manager 範本中部署資源的多個執行個體
此主題說明如何逐一查看您的「Azure 資源管理員」範本，以建立資源的多個執行個體。

## <a name="copy-and-copyindex"></a>複製和 copyindex
建立多個時間的資源需使用下列格式：

```json
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())", 
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

請注意，逐一查看的次數會在複製物件中指定：

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

計數值必須為不超過 800 的正整數。

請注意，每個資源的名稱均包含 `copyIndex()` 函式，並會傳回目前的反覆項目迴圈。

```json
"name": "[concat('examplecopy-', copyIndex())]",
```

如果您部署三個網站，這些網站將名為：

* examplecopy-0
* examplecopy-1
* examplecopy-2。

若要位移索引值，您可以傳遞如同 `copyIndex(1)` 的 copyIndex() 函式。 要執行的反覆項目數仍然在複製項目中指定，但 copyIndex 的值會由指定的值位移。 因此，使用和前一個範例相同的範本，但指定 copyIndex(1) 時，部署的三個網站將名為：

* examplecopy-1
* examplecopy-2
* examplecopy-3

Resource Manager 會以平行方式建立資源。 因此，不保證資源會循序建立。 若要循序建立重複列舉的資源，請參閱 [Azure Resource Manager 範本的循序迴圈](resource-manager-sequential-loop.md)。 

您只能將 copy 物件套用至最上層資源。 您不能將它套用至資源類型上的屬性或套用至子資源。 下列虛擬程式碼範例示範可將 copy 套用到的位置︰

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* Yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* No, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* No, copy cannot be applied here. The resource must be promoted to top-level. */ 
      }
    ]
  }
] 
```

若要使用子資源，請參閱為[子資源建立多個執行個體](#create-multiple-instances-of-a-child-resource)。

雖然您無法將複製套用到屬性，該屬性仍屬於屬性所在資源的反覆項目。 因此，您可以在屬性內使用 copyIndex() 來指定值。 若要建立多個屬性的值，請參閱[為資源類型的屬性建立多個執行個體](resource-manager-property-copy.md)。

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

請注意，`length` 函式係用來指定計數。 您可以提供陣列為 length 函數的參數。

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

## <a name="depend-on-resources-in-a-loop"></a>依迴圈中的資源而定
您可以透過使用 `dependsOn` 元素，讓某個資源在另一個資源之後才部署。 若要部署相依於迴圈中資源集合的資源時，請在 dependsOn 元素中提供複製迴圈的名稱。 下列範例示範如何在部署虛擬機器之前部署三個儲存體帳戶。 不會顯示完整的虛擬機器定義。 請注意，複製元素將名稱設定為 `storagecopy`，並將虛擬機器的 dependsOn 元素設定為 `storagecopy`。

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
您無法為子資源使用複製迴圈。 若要為通常定義為巢狀在另一個資源內的資源建立多個執行個體，您必須改為將該資源建立為最上層資源。 您可以透過類型和名稱屬性，定義和父資源之間的關聯性。

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

若要為資料集建立多個執行個體，請在其移至 Data Factory 外。 資料集必須位於和 Data Factory 相同的層級，但它仍是 Data Factory 的子資源。 您可以透過類型和名稱屬性保留資料集與資料處理站之間的關聯性。 由於無法再從類型位於範本中的位置來推斷類型，您必須以此格式提供完整的類型︰`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`。

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

## <a name="next-steps"></a>後續步驟
* 若要了解範本區段的相關資訊，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要循序建立重複列舉的資源，請參閱 [Azure Resource Manager 範本的循序迴圈](resource-manager-sequential-loop.md)。
* 若要了解如何部署範本，請參閱 [使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。


