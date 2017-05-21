---
title: "Azure Resource Manager 範本 - 以物件作為參數 | Microsoft Docs"
description: "說明如何擴充 Azure Resource Manager 範本的功能，以使用物件作為參數"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/01/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 617c24ea999aef78696ff08add4b9616e3dac589
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---objects-as-parameters"></a>Azure Resource Manager 範本功能的擴充模式 - 以物件作為參數

Azure Resource Manager 範本支援使用參數來指定值，以自訂資源的部署。 雖然此功能很實用，且可讓您建立複雜的部署，但單一範本最多只能使用 255 個參數。 如果您對資源中的每個屬性使用參數，且您擁有的是大型部署，則參數可能會用盡。

## <a name="create-object-as-parameter"></a>建立物件來作為參數

若要解決此問題，有一個方法是使用物件來作為參數。 其模式是將參數指定為範本中的物件，然後將該物件提供作為值或值陣列。 您使用 `parameter()` 函式和點運算子來參考其子屬性。 例如：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "VNetSettings":{"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[parameters('VNetSettings').name]",
      "location":"[resourceGroup().location]",
      "properties": {
          "addressSpace":{
              "addressPrefixes": [
                  "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
              ]
          },
          "subnets":[
              {
                  "name":"[parameters('VNetSettings').subnets[0].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
                  }
              },
              {
                  "name":"[parameters('VNetSettings').subnets[1].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
                  }
              }
          ]
        }
    }
  ],          
  "outputs": {}
}

```

對應的參數檔案看起來像這樣：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "VNetSettings":{
          "value":{
              "name":"VNet1",
              "addressPrefixes": [
                  { 
                      "name": "firstPrefix",
                      "addressPrefix": "10.0.0.0/22"
                  }
              ],
              "subnets":[
                  {
                      "name": "firstSubnet",
                      "addressPrefix": "10.0.0.0/24"
                  },
                  {
                      "name":"secondSubnet",
                      "addressPrefix":"10.0.1.0/24"
                  }
              ]
          }
      }
  }
}
```

在此範例中，所有針對 VNet 所指定的值都來自單一參數 `VNetSettings`。 此模式適合用來管理屬性值，因為您會將特定資源的所有值保留在單一物件中。 而且雖然這個範例使用物件來作為參數，但您也可以使用物件陣列來作為參數。 您會使用陣列索引來參考物件。

## <a name="use-object-instead-of-multiple-arrays"></a>使用物件而非多個陣列

您可能使用過類似模式來建立資源的多個執行個體，方法是建立多個屬性值陣列，並逐一查看每個陣列以選取值。 此模式適用於建立多個相同類型的資源，但不適用於建立子資源。 

此問題歸咎於兩個原因。 首先，Resource Manager 會嘗試平行部署子資源，但部署會在兩個子資源同時更新父系時失敗。 

其次，系統會以平行方式逐一查看每個屬性值陣列，如果每個陣列的外觀不同，系統就會發生錯誤。 例如，請考慮下列屬性陣列：

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two"
    ]
}
```

用來將這些值指派給複製迴圈內之屬性的典型模式，是使用 `variables()` 函式來存取屬性，並使用 `copyIndex()` 作為陣列的索引。 例如：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```
請注意，複製迴圈的 `count` 是根據 `firstProperty` 陣列中的屬性數目。 不過，`secondProperty` 陣列中的屬性數目不同。 此範本的驗證會失敗，因為 `secondProperty` 陣列的長度不同。

不過，如果您將所有屬性納入單一物件內，當其中有值遺漏時，您就可以更輕鬆地發現。 例如：

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

## <a name="try-the-template"></a>試用範本

如果您想要實驗這些範本，請遵循下列步驟︰

1.    請移至 Azure 入口網站，選取 [+] 圖示，並搜尋 「 範本部署 」 資源類型。 請在搜尋結果中找到並選取此資源類型。
2.    當您進入 [範本部署] 頁面時，選取 [建立] 按鈕。 這個按鈕會開啟 [自訂部署] 刀鋒視窗。
3.    選取 [編輯範本] 按鈕。
4.    請在右窗格中刪除空的範本。
5.    將範本樣本複製並貼到右窗格。
6.    選取 [儲存] 按鈕。
7.    當您返回 [自訂部署] 窗格時，選取 [編輯參數] 按鈕。
8.  在 [編輯參數] 刀鋒視窗中，刪除現有範本。
9.  複製並貼上前面的參數範本樣本。
10. 選取 [儲存] 按鈕，此動作會讓您返回 [自訂部署] 刀鋒視窗。
11. 在 [自訂部署] 刀鋒視窗上選取您的訂用帳戶，接著新建資源群組或使用現有資源群組，然後選取位置。 檢閱條款及條件，然後選取 [我同意] 核取方塊。
12.    選取 [購買] 按鈕。

## <a name="next-steps"></a>後續步驟

如果您需要的參數數量超過每個部署允許的上限 (255 個)，使用此模式即可在範本中指定較少的參數。 您也可以使用此模式，以便更輕鬆地管理資源屬性，然後使用循序迴圈模式來部署屬性而不發生衝突。

* 如需 `parameter()` 函式和陣列使用方式的簡介，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
* 此模式也可以在[範本建置區塊專案](https://github.com/mspnp/template-building-blocks)與 [Azure 參考架構](/azure/architecture/reference-architectures/)中實作。
