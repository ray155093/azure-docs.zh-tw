---
title: "Azure Resource Manager 範本 - 更新資源 | Microsoft Docs"
description: "說明如何擴充 Azure Resource Manager 範本的功能，以更新資源"
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/02/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7807b3f6135ede80cd7e7e6486364482cde72e29
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---updating-a-resource"></a>擴充 Azure Resource Manager 範本功能的模式 - 更新資源

在某些情況下，您必須在部署期間更新資源。 當您必須等到其他相依資源建立後才能指定某項資源的所有屬性時，就會遇到這種情況。 例如，如果您建立負載平衡器的後端集區，您可能會更新虛擬機器 (VM) 上的網路介面 (NIC)，以將這些介面納入後端集區。 Resource Manager 支援在部署期間更新資源，但您必須正確設計範本以免發生錯誤，並確保會以更新的方式來處理部署。

## <a name="understand-the-pattern"></a>了解模式

首先，您必須在範本中參考資源一次以建立資源，但之後必須以相同的名稱參考資源，才能在稍後對資源進行更新。 不過，如果這兩個資源在範本中擁有相同名稱，Resource Manager 就會擲回例外狀況。 為了避免這個錯誤，請在第二個範本中指定更新後的資源，並使用 `Microsoft.Resources/deployments` 資源類型，將更新後的資源連結或納入為子範本。

其次，在第二個範本中，您必須指定要變更的現有屬性名稱，或為要新增的屬性指定新名稱。 然後，您也必須指定原始屬性和其原始值。 如果您無法提供原始的屬性和值，Resource Manager 會假設您想要建立新的資源，並會刪除原始資源。 它會以新資源 (只包含您所指定的新屬性) 來取代原始資源。

最後，您必須讓資源相依於您想要部署的所有相關資源。 此相依性可確保資源會以正確順序來建立。 順序如下︰

1. 建立資源
2. 建立相依資源
3. 以相依資源 (來自步驟 2) 的值來更新資源 (來自步驟 1)

## <a name="example-template"></a>範本範例

下列範本範例會示範這個模式。 它會部署名為 `firstVNet` 的虛擬網路 (VNet)，此網路具有一個名為 `firstSubnet` 的子網路。 接著，它會部署名為 `nic1` 的虛擬網路介面 (NIC)，並讓它與子網路相關聯。 然後，名為 `updateVNet` 的部署資源會納入巢狀範本，此範本會參考 `firstVNet` 資源的名稱。 

請看看此資源的 `addressSpace` 屬性和 `subnets` 屬性。 請注意，`addressSpace` 值會設定為與 `firstVNet` 資源部署物件上相同的屬性值。 在 `subnets` 陣列中，會以同樣方式設定 `firstSubnet` 的值。 因為所有原始的 `firstVNet` 屬性皆已指定，Resource Manager 會更新 Azure 中的資源。 在此案例中，更新內容是新增名為 `secondSubnet` 的全新子網路。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

## <a name="try-the-template"></a>試用範本

如果您想要實驗此範本，請遵循下列步驟︰

1.    請移至 Azure 入口網站，選取 [+] 圖示，並搜尋 「 範本部署 」 資源類型。 請在搜尋結果中找到並選取此資源類型。
2.    當您進入 [範本部署] 頁面時，選取 [建立] 按鈕，此按鈕會開啟 [自訂部署] 刀鋒視窗。
3.    選取 [編輯] 圖示。
4.    刪除空白範本。
5.    將前述的樣本範本複製並貼上至右窗格。
6.    選取 [儲存] 按鈕。
7.    您會返回 [自訂部署] 窗格，但這次窗格中會出現一些下拉式方塊。 請選取您的訂閱，您可以建立新的或使用現有的資源群組，並選取一個位置。 檢閱條款及條件，然後選取 [我同意] 按鈕。
8.    選取 [購買] 按鈕。

當部署完成之後，開啟您在入口網站中指定的資源群組。 您會看到名為 `firstVNet` 的 VNet 和名為 `nic1` 的 NIC。 按一下 `firstVNet`，然後再按一下 `subnets`。 您會看到原先建立的 `firstSubnet`，並看到 `updateVNet` 資源中新增的 `secondSubnet`。 

![原始子網路和更新後的子網路](./media/resource-manager-update/firstVNet-subnets.png)

然後，返回資源群組，並依序按一下 `nic1` 和 `IP configurations`。 在 `IP configurations` 區段中，`Subnet` 會設為 `firstSubnet (10.0.0.0/24)`。 

![nic1 IP 組態設定](./media/resource-manager-update/nic1-ipconfigurations.png)

原始的 `firstVNet` 已更新而非重新建立。 如果 `firstVNet` 已重新建立，`nic1` 不會與 `firstVNet` 相關聯。

## <a name="next-steps"></a>後續步驟

您可以在範本中使用這種模式來重新指定您想要更新的資源原始屬性。 在連結或巢狀的範本中，使用 `Microsoft.Resources/deployments` 資源類型來指定更新資源。

* 如需 `reference()` 函式的簡介，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
* 此模式也可以在[範本建置區塊專案](https://github.com/mspnp/template-building-blocks)與 [Azure 參考架構](/azure/architecture/reference-architectures/)中實作。
