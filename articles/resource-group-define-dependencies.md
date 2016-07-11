<properties
   pageTitle="資源管理員範本中的相依性 | Microsoft Azure"
   description="說明如何在部署期間，將某個資源設定為相依於另一個資源，確保以正確的順序部署資源。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# 定義 Azure 資源管理員範本中的相依性

針對指定的資源，可能會有部署資源之前必須存在的其他資源。例如，SQL Server 必須存在，才能嘗試部署 SQL 資料庫。您可以將一個資源標示為相依於其他資源，來定義此關聯性。通常，您會使用 **dependsOn** 元素來定義相依性，但也可以透過 **reference** 函式予以定義。

資源管理員會評估資源之間的相依性，並依其相依順序進行部署。資源若不互相依賴，資源管理員就會平行部署資源。

## dependsOn

在您的範本中，dependsOn 元素可讓您將一個資源定義為與一個或多個資源相依。它的值可以是資源名稱的逗號分隔清單。

下列範例示範虛擬機器擴展集，其相依於負載平衡器、虛擬網路，以及建立多個儲存體帳戶的迴圈。這些其他資源不會顯示在下面，但它們需要存在於範本的其他位置。

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
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

如果您需要定義某項資源和透過複製迴圈所建立之資源之間的相依性 (如上所示)，可以將 dependsOn 元素設定為迴圈的名稱。例如，請參閱[在 Azure 資源管理員中建立多個資源的執行個體](resource-group-create-multiple.md)。

雖然您可能比較傾向於使用 dependsOn 對應您的資源之間的相依性，但是請務必了解為什麼您要這麼做，因為這可能會影響您部署的效能。例如，如果您這麼做是因為您想要記載資源互連的方式，則 dependsOn 並不是適當的方法。dependsOn 的生命週期只能用於部署，而且不適用於後續部署。一旦部署之後，就沒有任何方法可以查詢這些相依性。使用 dependsOn，表示您冒著影響效能的風險，可能會讓您不小心讓部署引擎轉為使用它可能會有的平行處理原則。若要記載資源之間的關聯性並提供查詢功能，您應該改用[連結資源](resource-group-link-resources.md)。

## 子資源

resources 屬性可讓您指定與所定義的資源相關的子資源。子資源僅能定義為 5 個層級的深度。請務必注意，在子資源與父資源之間並不會建立隱含的相依性。如果您需要在父資源之後部署子資源，您必須使用 dependsOn 屬性明確地敘述該相依性。

每個父資源只接受特定的資源類型做為子資源。可接受的資源類型是在父資源的[範本結構描述](https://github.com/Azure/azure-resource-manager-schemas)中指定。子資源類型的名稱包含父資源類型的名稱，例如 **Microsoft.Web/sites/config** 和 **Microsoft.Web/sites/extensions** 兩者皆為 **Microsoft.Web/sites** 的子資源。

下列範例示範 SQL 伺服器和 SQL 資料庫。請注意，SQL 資料庫與 SQL 伺服器之間定義明確相依性，即使資料庫是伺服器的子系也是一樣。

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


## reference 函式

reference 函式可讓運算式從其他 JSON 名稱和值組或執行階段資源衍生其值。reference 運算式會隱含地宣告某個資源相依於另一個資源。以下 **propertyPath** 所代表的屬性是選擇性的，如果未指定，則參考資源。

    reference('resourceName').propertyPath

您可以使用此元素或 dependsOn 元素指定相依性，但是您不需要針對相同的相依資源使用兩者。本指導方針是使用隱含的參考以避免不小心讓不必要的 dependsOn 元素防止部署引擎以平行方式進行部署的風險。

若要深入了解，請參閱 [reference 函數](resource-group-template-functions.md#reference)。

## 後續步驟

- 若要了解如何建立 Azure 資源管理員範本，請參閱[撰寫範本](resource-group-authoring-templates.md)。
- 如需在範本中可用函式的清單，請參閱[範本函式](resource-group-template-functions.md)。

<!---HONumber=AcomDC_0629_2016-->