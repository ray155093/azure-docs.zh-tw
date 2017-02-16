---
title: "Resource Manager 範本中的相依性 | Microsoft Docs"
description: "說明如何在部署期間，將某個資源設定為相依於另一個資源，確保以正確的順序部署資源。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5f810a46db4deed9c31db4f7c072c48b0817ebc4
ms.openlocfilehash: 35ce1f12a3de0a41d400cceebe6aefbadbe51528


---
# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>定義 Azure 資源管理員範本中的相依性
針對指定的資源，可能會有部署資源之前必須存在的其他資源。 例如，SQL Server 必須存在，才能嘗試部署 SQL 資料庫。 您可以將一個資源標示為相依於其他資源，來定義此關聯性。 通常，您會使用 **dependsOn** 元素來定義相依性，但也可以透過 **reference** 函式予以定義。 

資源管理員會評估資源之間的相依性，並依其相依順序進行部署。 資源若不互相依賴，資源管理員就會平行部署資源。 您只需要針對部署在相同範本中的資源定義相依性。 

## <a name="dependson"></a>dependsOn
在您的範本內，dependsOn 元素可讓您定義一個資源作為一或多個資源的相依項目。 其值可以是以逗號分隔的資源名稱清單。 

下列範例示範一個虛擬機器擴展集，此擴展集依存於負載平衡器、虛擬網路，以及會建立多個儲存體帳戶的迴圈。 這些其他資源不會顯示在下列範例中，但是必須存在於範本中其他的位置。

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[variables('loadBalancerName')]",
        "[variables('virtualNetworkName')]"
      ],
      ...
    }

在上述範例中，相依性是包含在透過名為 **storageLoop**複製迴圈所建立的資源上。 例如，請參閱 [在 Azure 資源管理員中建立多個資源的執行個體](resource-group-create-multiple.md)。

當定義相依性時，您可以包含資源提供者命名空間和資源類型，以避免模稜兩可。 比方說，若要釐清可能有和其他資源名稱相同的負載平衡器和虛擬網路，請使用下列格式：

    "dependsOn": [
      "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
      "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
    ] 

雖然您可能比較傾向於使用 dependsOn 來對應資源之間的關聯性，但是請務必了解為什麼您要這麼做，因為這可能會影響您部署的效能。 例如，若是要記載資源互連的方式，dependsOn 並不是適當的方法。 在部署之後，您便無法查詢 dependsOn 元素中定義了哪些資源。 使用 dependsOn 可能會影響部署時間，因為 Resource Manager 不會平行部署具有相依性的兩個資源。 若要記載資源之間的關聯性，請改用 [資源連結](resource-group-link-resources.md)。

## <a name="child-resources"></a>子資源
resources 屬性可讓您指定與所定義的資源相關的子資源。 定義子資源時，深度只能有 5 層。 請務必注意，在子資源與父資源之間並不會建立隱含的相依性。 如果您需要在父資源之後部署子資源，您必須使用 dependsOn 屬性明確地敘述該相依性。 

每個父資源只接受特定的資源類型做為子資源。 可接受的資源類型是在父資源的 [範本結構描述](https://github.com/Azure/azure-resource-manager-schemas) 中指定。 子資源類型的名稱包含父資源類型的名稱，例如 **Microsoft.Web/sites/config** 和 **Microsoft.Web/sites/extensions** 兩者皆為 **Microsoft.Web/sites** 的子資源。

下列範例示範 SQL 伺服器和 SQL 資料庫。 請注意，SQL 資料庫與 SQL 伺服器之間定義明確相依性，即使資料庫是伺服器的子系也是一樣。

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>reference 函式
[reference 函式](resource-group-template-functions.md#reference) 可讓運算式從其他 JSON 名稱和值組或執行階段資源衍生其值。 reference 運算式會隱含地宣告某個資源相依於另一個資源。 

    reference('resourceName').propertyPath

您可以使用此元素或 dependsOn 元素指定相依性，但是您不需要針對相同的相依資源使用兩者。 請儘量使用隱含的參考，以避免不小心新增不必要的相依性。

若要深入了解，請參閱 [reference 函數](resource-group-template-functions.md#reference)。

## <a name="next-steps"></a>後續步驟
* 若要了解如何建立 Azure 資源管理員範本，請參閱 [撰寫範本](resource-group-authoring-templates.md)。 
* 如需在範本中可用函式的清單，請參閱 [範本函式](resource-group-template-functions.md)。




<!--HONumber=Nov16_HO4-->


