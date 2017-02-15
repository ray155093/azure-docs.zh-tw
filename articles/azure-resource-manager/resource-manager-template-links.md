---
title: "連結資源的 Resource Manager 範本 | Microsoft Docs"
description: "顯示可透過範本在相關資源之間部署連結的資源管理員結構描述。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 48a13b1a-3208-4f91-ba85-bda2a0e22605
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: dcac45042b2511ae2b82ecf8278e9873c6f8c9b9


---
# <a name="resource-links-template-schema"></a>資源連結範本結構描述
這會在兩個資源之間建立連結。 該連結會套用至稱為「來源資源」的資源。 連結中的第二個資源則稱為「目標資源」。

## <a name="schema-format"></a>結構描述格式
若要建立連結，請將下列結構描述新增到範本的資源區段。

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>值
下表描述您在結構描述中必須設定的值。

| 名稱 | 值 |
| --- | --- |
| 類型 |例舉<br />必要<br />**{namespace}/{type}/providers/links**<br /><br />要建立的資源類型。 {namespace} 和 {type} 值指的是來源資源的提供者命名空間和資源類型。 |
| apiVersion |例舉<br />必要<br />**2015-01-01**<br /><br />要用來建立資源的應用程式開發介面 (API) 版本。 |
| 名稱 |String<br />必要<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> 最多 64 個字元，而且不能包含 <、>、%、&、? 或任何控制字元。<br /><br />同時指定來源資源名稱和連結名稱的值。 |
| dependsOn |陣列<br />選用<br />以逗號分隔的資源名稱或資源唯一識別碼清單。<br /><br />此連結所依存的資源集合。 若您所連結的資源被部署到相同的範本，請在此元素中包含那些資源的名稱，以確保它們會被優先部署。 |
| properties |Object<br />必要<br />[屬性物件](#properties)<br /><br />能識別要連結的資源，以及該連結之相關資訊的物件。 |

<a id="properties" />

### <a name="properties-object"></a>屬性物件
| 名稱 | 值 |
| --- | --- |
| targetId |String<br />必要<br />**{resource id}**<br /><br />要連結之目標資源的識別碼。 |
| 版本 |String<br />選用<br />最多 512 個字元<br /><br />鎖定的描述。 |

## <a name="how-to-use-the-link-resource"></a>如何使用連結資源
當兩個資源的相依性在部署後持續時，在它們之間套用連結。 例如，某個 app 可能會連接到位於不同資源群組的資料庫。 您可以藉由在該 app 和資料庫之間建立連結，來定義該相依性。 連結能讓您記錄兩個資源之間的關聯性。 稍後您或組織內的其他人可以向資源查詢連結，以探索該資源和其他資源一同運作的方式。

所有已連結的資源都必須屬於同一個訂用帳戶。 每個資源都可以連結至其他 50 個資源。 如果任何已連結的資源被刪除或移動，連結擁有者必須清除剩餘的連結。

若要透過 REST 使用連結，請參閱 [連結的資源 (英文)](https://msdn.microsoft.com/library/azure/mt238499.aspx)。

使用下列 Azure PowerShell 命令來查看訂用帳戶中的所有連結。 您可以提供其他參數來限制結果。

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>範例
下列範例會將唯讀鎖定套用到 Web 應用程式。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>快速入門範本
下列快速入門範本會利用連結部署資源。

* [利用邏輯應用程式警示加入佇列](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
* [利用邏輯應用程式警示放入 Slack](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
* [以現有閘道佈建 API 應用程式](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
* [以新的閘道佈建 API 應用程式](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
* [使用範本建立邏輯應用程式與 API 應用程式](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
* [會在引發警示時傳送文字訊息的邏輯應用程式](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)

## <a name="next-steps"></a>後續步驟
* 如需範本結構的相關資訊，請參閱 [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。




<!--HONumber=Nov16_HO3-->


