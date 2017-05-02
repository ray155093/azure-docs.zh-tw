---
title: "Azure 範本中的循序迴圈 | Microsoft Docs"
description: "說明如何擴充 Azure Resource Manager 範本的功能，以在部署多個資源類型的執行個體時，實作循序迴圈。"
services: guidance, azure-resource-manager
documentationcenter: na
author: petertaylor9999
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 04/18/2017
ms.author: petertay
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 44d59488600e05d6ae80a169b5d682e8fa94ccc5
ms.lasthandoff: 04/18/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---sequential-looping"></a>擴充 Azure Resource Manager 範本功能的模式 - 循序迴圈

Azure Resource Manager 範本支援透過複製迴圈，部署一組類似的資源。 複製迴圈中的資源物件可以用來逐一查看字串陣列，然後用來產生資源的唯一名稱。 資源物件中的複製迴圈也可以用來逐一查看描述資源的變數陣列。

這些模式都可以正常運作，但只限於在群組中的每個成員間沒有相依性時可以正常運作。 在反覆項目迴圈中，Resource Manager 會嘗試平行部署資源。 如果第一個資源相依於第二個，Resource Manager 若先部署了第二個資源，就可能造成部屬失敗。

真正的問題是，Resource Manager 目前在反覆項目迴圈中不支援 `dependsOn`。 不過，您可以使用現有的 Resource Manager 功能和一些有創意的資源命名來實作這項功能。 

## <a name="sequential-looping-pattern"></a>循序迴圈模式

模式如下︰ 第一個資源使用串連名稱前置詞和 `0`，或是使用第一個迴圈的索引命名。 第二個資源包含複製迴圈，而且在資源迴圈中，下一個資源的名稱是 `copyIndex(1)` 函式產生的串連名稱前置詞，並將 1 加入目前的 `copyIndex()`。 第二個資源也包括參考串連名稱前置詞與 `copyIndex()` 函式結果的 `dependsOn` 元素。 這個方法會建立下一個資源與上一個資源之間的 `dependsOn` 關聯性。 Resource Manager 會等到上一個資源部署完成後才部署下一個資源。

下列範本會示範這個模式。 Microsoft.Resources/deployments 資源類型屬於巢狀範本，實際上不部署任何項目。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "variables": {
    "count": "[sub(parameters('numberToDeploy'), 1)]"
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "loop-0 "
            }
          }
        }
      }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex(1))]",
      "dependsOn": [
        "[concat('loop-', copyIndex())]"
      ],
      "copy": {
        "name": "iterator",
        "count": "[variables('count')]"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "[concat(reference(concat('loop-',copyIndex())).outputs.collection.value,'loop-',copyIndex(1), ' ')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference(concat('loop-',variables('count'))).outputs.collection.value]"
    }
  }
}

```
在此範本中，第一個資源物件名為 `loop-0`。 然後，在第二個資源物件中，下一個資源名稱是 `loop-` 與 `copyIndex(1)` 函式的結果 (`loop-1`) 的串連。 `dependsOn` 元素參考了上一個資源，因為其為 `loop-` 與 `copyIndex()` 函式的結果 (`loop-0`) 的串連。 第二個資源物件中的模式會重複直到達到 `count`，並以名為 `loop-4`，且`dependsOn``loop-3` 的資源做結束。 請注意，`count` 是一個從 `numberToDeploy` 參數中減去 `1` 的變數，以確保從零開始的計數正確。

## <a name="try-the-template"></a>試用範本

如果您想要實驗此範本，請遵循下列步驟︰

1.    請移至 Azure 入口網站，選取 [+] 圖示，並搜尋 「 範本部署 」 資源類型。 請在搜尋結果中找到並選取此資源類型。
2.    當您至「範本部署」頁面時，選取 [建立] 按鈕。 您將移至 「 自訂部署 」 刀鋒視窗，並會看到該範本不含任何資源。
3.    請選取 [編輯] 圖示。
4.    請在右窗格中刪除空的範本。
5.    將前述的樣本範本複製並貼上至右窗格。
6.    請選取 [儲存] 按鈕。
7.    您會回到 「 自訂部署 」 窗格中，但這次會出現一些下拉式清單方塊。 請選取您的訂閱，您可以建立新的或使用現有的資源群組，並選取一個位置。 檢閱條款和條件，然後按一下 [我同意] 按鈕。
8.    請按一下 [購買] 按鈕。

若要驗證資源有循序部署，請選取 [資源群組]，然後再選取您先前選取的資源群組。 在資源群組刀鋒視窗中，按一下 [部署] 按鈕，您會看到資源循序部署與對應的時間戳記。

![Azure Resource Manager 中的循序迴圈部署範本](./media/resource-manager-sequential-loop/deployments.png)

## <a name="next-steps"></a>後續步驟

請透過將您的資源新增至巢狀範本以在您的範本中使用此模式。 您可以直接 `Microsoft.Resources/deployments` 資源的範本元素中撰寫，或是使用 `templateLink` 元素連結。 此範例中的資源類型是巢狀範本，但您可以部署任何資源類型。 唯一的例外狀況是，在反覆項目迴圈內無法參考子資源。

* 如需建立多個資源的執行個體，請參閱[在 Azure Resource Manager 範本中部署資源的多個執行個體](resource-group-create-multiple.md)。
* 此模式也可以在[範本建置區塊專案](https://github.com/mspnp/template-building-blocks)與 [Azure 參考架構](/azure/architecture/reference-architectures/)中實作。
