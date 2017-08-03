---
title: "使用 Resource Manager 範本建立活動記錄警示 | Microsoft Docs"
description: "在 Azure 資源建立時取得通知。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 26b140fef46a176b21bddbd7588543e71c251ed6
ms.contentlocale: zh-tw
ms.lasthandoff: 07/18/2017

---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立活動記錄警示
本文章將說明如何使用 [Azure Resource Manager 範本](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates)設定活動記錄警示。 這可讓您在自動化部署程序建立資源時，自動在您的資源上設定警示。

基本步驟如下：

1.  建立一個以描述如何建立活動記錄警示的 JSON 檔案為形式的範本。
2.  [使用任何部署方法部署範本。](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

## <a name="resource-manager-template-for-an-activity-log-alert"></a>活動記錄警示的 Resource Manager 範本
若要使用 Resource Manager 範本建立活動記錄警示，您要建立 `microsoft.insights/activityLogAlerts` 類型的資源，並填入所有相關的屬性。 以下是建立活動記錄警示的範本。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

您也可以[前往我們的快速入門資源庫](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights)，取得活動記錄警示範本的一些範例。

## <a name="next-steps"></a>後續步驟
- 深入了解[警示](monitoring-overview-alerts.md)  
- 如何[使用 Resource Manager 範本新增動作群組](monitoring-create-action-group-with-resource-manager-template.md)
- [建立活動記錄警示以監視訂用帳戶的所有自動調整引擎作業。](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [建立活動記錄警示以監視訂用帳戶中所有失敗的相應縮小/相應放大自動調整作業](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

