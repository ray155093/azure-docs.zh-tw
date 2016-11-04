---
title: 使用 Resource Manager 範本自動啟用診斷設定 | Microsoft Docs
description: 了解如何使用 Resource Manager 範本來建立診斷設定，以讓您將診斷記錄檔串流至事件中樞，或將它們儲存在儲存體帳戶中。
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem

---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>使用 Resource Manager 範本在建立資源時自動啟用診斷設定
在本文中，我們示範如何在建立資源時使用 [Azure Resource Manager 範本](../resource-group-authoring-templates.md) 設定診斷設定。 這可讓您在建立資源時，自動開始將您的診斷記錄檔和度量串流至事件中樞、將它們封存在儲存體帳戶中，或將它們傳送至 Log Analytics。

使用 Resource Manager 範本啟用診斷記錄檔的方法，取決於資源類型。

* **非計算** 資源 (例如，網路安全性群組、Logic Apps、自動化) 使用 [這篇文章中所述的診斷設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)。
* **計算** 資源 (以 WAD/LAD 為基礎) 使用 [本文中所述的WAD/LAD 組態檔](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)。

在本文中，我們會說明如何使用這兩種方法來設定診斷。

基本步驟如下：

1. 建立一個描述如何建立資源的 JSON 檔案做為範本，然後啟用診斷功能。
2. [使用任何部署方法部署範本](../resource-group-template-deploy.md)。

以下提供產生非計算和計算資源所需的範本 JSON 檔案的範例。

## <a name="non-compute-resource-template"></a>非計算資源範本
如是非計算資源，您需要做兩件事︰

1. 將參數加入儲存體帳戶名稱、服務匯流排規則識別碼，和/或 OMS Log Analytics 工作區識別碼的參數 blob (可啟用儲存體帳戶中診斷記錄檔的封存、串流記錄檔至事件中樞，和/或將記錄檔傳送至 Log Analytics)。
   
    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. 在您要啟用診斷記錄檔的資源的資源陣列中，加入 `[resource namespace]/providers/diagnosticSettings`類型的資源。
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

診斷設定的屬性 blob 遵循 [這篇文章中所述的格式](https://msdn.microsoft.com/library/azure/dn931931.aspx)。

以下的完整範例會建立網路安全性群組，並開啟串流至事件中樞和儲存體帳戶中的儲存體。

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>計算資源範本
若要啟用計算資源 (例如虛擬機器或 Service Fabric 叢集) 的診斷功能，您需要︰

1. 將 Azure 診斷擴充功能加入 VM 資源定義。
2. 指定儲存體帳戶和/或事件中樞做為參數。
3. 將 WADCfg XML 檔案的內容加入 XMLCfg 屬性，適當地逸出所有 XML 字元。

> [!WARNING]
> 這最後一個步驟要做得正確可能會有點棘手。 [請參閱這篇文章](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) 的範例，將診斷組態結構描述分割成正確逸出和格式正確的變數。
> 
> 

在 [本文件中](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)中會說明整個程序，包括範例。

## <a name="next-steps"></a>後續步驟
* [深入了解 Azure 診斷記錄檔](monitoring-overview-of-diagnostic-logs.md)
* [將 Azure 診斷記錄檔串流至事件中樞](monitoring-stream-diagnostic-logs-to-event-hubs.md)

<!--HONumber=Oct16_HO2-->


