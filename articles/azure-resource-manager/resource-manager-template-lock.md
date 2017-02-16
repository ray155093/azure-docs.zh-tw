---
title: "資源鎖定的 Resource Manager 範本 | Microsoft Docs"
description: "說明可透過範本部署資源鎖定的資源管理員結構描述。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>資源鎖定範本結構描述
在資源及其子資源上建立鎖定。

## <a name="schema-format"></a>結構描述格式
若要建立鎖定，請將下列結構描述新增到範本的資源區段。

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>值
下表描述您在結構描述中必須設定的值。

| 名稱 | 必要 | 說明 |
| --- | --- | --- |
| 類型 |是 |要建立的資源類型。<br /><br />針對資源：<br />**{namespace}/{type}/providers/locks**<br /><br/>針對資源群組：<br />**Microsoft.Authorization/locks** |
| apiVersion |是 |要用來建立資源的應用程式開發介面 (API) 版本。<br /><br />使用︰<br />**2015-01-01**<br /><br /> |
| 名稱 |是 |指定要鎖定之資源和鎖定名稱的值。 最多可有 64 個字元，而且不能包含 <、>、%、&、? 或任何控制字元。<br /><br />針對資源：<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />針對資源群組：<br />**{lockname}** |
| dependsOn |否 |以逗號分隔的資源名稱或資源唯一識別碼清單。<br /><br />此鎖定所依存的資源集合。 如果您正在鎖定的資源是部署在同一個範本中，請在此元素中包含資源名稱，確保會先部署該資源。 |
| properties |是 |識別鎖定類型和鎖定附註的物件。<br /><br />請參閱[屬性物件](#properties-object)。 |

### <a name="properties-object"></a>屬性物件
| 名稱 | 必要 | 說明 |
| --- | --- | --- |
| 層級 |是 |要套用至範圍的鎖定類型。<br /><br />**CannotDelete** - 使用者可修改資源，但不能將它刪除。<br />**ReadOnly** - 使用者可以讀取資源，但是無法刪除資源或對其執行任何動作。 |
| 版本 |否 |鎖定的描述。 最多可有 512 個字元。 |

## <a name="how-to-use-the-lock-resource"></a>如何使用鎖定資源
您可以將此資源新增至範本，以避免在資源上進行指定的動作。 鎖定會套用到所有的使用者和群組。

若要建立或刪除管理鎖定，您必須擁有 **Microsoft.Authorization/*** 或 **Microsoft.Authorization/locks/*** 動作的存取權。在內建角色中，只有 **擁有者** 和 **使用者存取管理員** 被授與這些動作的存取權。 如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

鎖定會套用至指定的資源和任何子資源。

您可以使用 PowerShell 命令 **Remove-AzureRmResourceLock** 或使用 REST API 的 [刪除作業](https://msdn.microsoft.com/library/azure/mt204562.aspx) 移除鎖定。

## <a name="examples"></a>範例
下列範例會將 cannot-delete 鎖定套用到 Web 應用程式。

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
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

接下來的範例會將 cannot-delete 鎖定套用到資源群組。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>後續步驟
* 如需範本結構的相關資訊，請參閱 [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
* 如需鎖定的詳細資訊，請參閱 [使用 Azure 資源管理員鎖定資源](resource-group-lock-resources.md)。




<!--HONumber=Nov16_HO3-->


