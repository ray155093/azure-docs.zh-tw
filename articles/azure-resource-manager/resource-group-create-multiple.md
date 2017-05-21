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
ms.date: 05/11/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e98fa067c0ed385fe20f66645311c9fd51cd6456
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>在 Azure Resource Manager 範本中部署資源或屬性的多個執行個體
此主題說明如何逐一查看您的「Azure 資源管理員」範本，以建立資源的多個執行個體。

## <a name="resource-iteration"></a>資源反覆項目
若要建立多個資源類型的執行個體，請將 `copy` 元素新增至資源類型。 在複製元素中，您可以指定反覆項目的數目以及此迴圈的名稱。 計數值必須為不超過 800 的正整數。 Resource Manager 會以平行方式建立資源。 因此，不保證資源會循序建立。 若要循序建立重複列舉的資源，請參閱 [Azure Resource Manager 範本的循序迴圈](resource-manager-sequential-loop.md)。 

建立多個時間的資源需使用下列格式：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

請注意，每個資源的名稱均包含 `copyIndex()` 函式，並會傳回目前的反覆項目迴圈。 `copyIndex()`是以零為基礎。 因此，下列範例：

```json
"name": "[concat('storage', copyIndex())]",
```

會建立這些名稱︰

* storage0
* storage1
* storage2.

若要位移索引值，您可以傳遞 copyIndex() 函式中的值。 要執行的反覆項目數仍然在複製項目中指定，但 copyIndex 的值會由指定的值位移。 因此，下列範例：

```json
"name": "[concat('storage', copyIndex(1))]",
```

會建立這些名稱︰

* storage1
* storage2
* storage3

使用陣列時，複製作業會有幫助，因為您可以逐一查看陣列中的每個項目。 使用陣列上的 `length` 函式指定反覆項目的計數，並使用 `copyIndex` 來擷取陣列中目前的索引。 因此，下列範例：

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

會建立這些名稱︰

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="depend-on-resources-in-a-loop"></a>依迴圈中的資源而定
您可以透過使用 `dependsOn` 元素，讓某個資源在另一個資源之後才部署。 若要部署相依於迴圈中資源集合的資源時，請在 dependsOn 元素中提供複製迴圈的名稱。 下列範例示範如何在部署虛擬機器之前部署三個儲存體帳戶。 不會顯示完整的虛擬機器定義。 請注意，複製元素將名稱設定為 `storagecopy`，並將虛擬機器的 dependsOn 元素設定為 `storagecopy`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
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


