---
title: "OMS 解決方案中的自動化資源 | Microsoft Docs"
description: "OMS 中的解決方案通常會在 Azure 自動化中包含 Runbook，以便自動執行一些程序，例如收集及處理監視資料。  本文說明如何在解決方案中包含 Runbook 與其相關資源。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>OMS 解決方案中的自動化資源 (預覽)
> [!NOTE]
> 這是在 OMS 中建立管理解決方案 (目前處於預覽狀態) 的預備文件。 以下所述的任何結構描述可能會有所變更。   
> 
> 

[OMS 中的管理解決方案](operations-management-suite-solutions.md)通常會在 Azure 自動化中包含 Runbook，以便自動執行一些程序，例如收集及處理監視資料。  除了 Runbook，自動化帳戶還包含一些資產，例如支援解決方案中所用 Runbook 的變數和排程。  本文說明如何在解決方案中包含 Runbook 與其相關資源。

> [!NOTE]
> 本文中的範例使用管理解決方案所必要或通用的參數和變數，如[在 Operations Management Suite (OMS) 中建立管理解決方案](operations-management-suite-solutions-creating.md)所述。 
> 
> 

## <a name="prerequisites"></a>必要條件
本文假設您已經熟悉如何[建立管理解決方案](operations-management-suite-solutions-creating.md)和方案檔的結構。

## <a name="samples"></a>範例
您可以從 [GitHub 中的快速入門範本](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates)取得自動化資源的範例 Resource Manager 範本。

## <a name="automation-account"></a>自動化帳戶
Azure 自動化中的所有資源都會包含在[自動化帳戶](../automation/automation-security-overview.md#automation-account-overview)中。  如 [OMS 工作區和自動化帳戶](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account)所述，自動化帳戶不包含在管理解決方案中，但是在安裝解決方案前就必須存在。  如果無法使用，則解決方案會安裝失敗。

其自動化帳戶的名稱位於每個自動化資源的名稱中。  這可在具有 **accountName** 參數的解決方案中完成，如下列 Runbook 資源範例所示。

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
[Azure 自動化 Runbook](../automation/automation-runbook-types.md) 資源的類型為 **Microsoft.Automation/automationAccounts/runbooks**，且具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| runbookType |指定 Runbook 的類型。 <br><br> Script - PowerShell 指令碼 <br>PowerShell - PowerShell 工作流程 <br> GraphPowerShell - 圖形化 PowerShell 指令碼 Runbook <br> GraphPowerShellWorkflow - 圖形化 PowerShell 工作流程 Runbook |
| logProgress |指定是否應針對 Runbook 產生[進度記錄](../automation/automation-runbook-output-and-messages.md)。 |
| logVerbose |指定是否應針對 Runbook 產生[詳細資訊記錄](../automation/automation-runbook-output-and-messages.md)。 |
| 說明 |Runbook 的選擇性說明。 |
| publishContentLink |指定 Runbook 的內容。 <br><br>uri - 指定 Runbook 的內容 Uri。  這會是 PowerShell 和 Script Runbook 的 .ps1 檔案，以及 Graph Runbook 的已匯出圖形化 Runbook 檔案。  <br> 版本 - 您自己追蹤的 Runbook 版本。 |

Runbook 資源的範例如下。  在此情況下，它會從 [PowerShell 資源庫](https://www.powershellgallery.com)擷取 Runbook。

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>啟動 Runbook
有兩種方法可以在管理方案中啟動 Runbook。

* 若要在安裝解決方案時啟動 Runbook，請建立[作業資源](#automation-jobs)，如下所述。
* 若要依照排程啟動 Runbook，請建立 [排程資源](#schedules)，如下所述。 

## <a name="automation-jobs"></a>自動化作業
若要在安裝管理解決方案時啟動 Runbook，您可建立**作業**資源。  作業資源的類型為 **Microsoft.Automation/automationAccounts/jobs**，且具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| Runbook |包含 Runbook 名稱的單一**名稱**實體。 |
| 參數 |Runbook 所需之每個參數的實體。 |

作業包含 Runbook 名稱和任何要傳送至 Runbook 的參數值。  作業必須[相依於](operations-management-suite-solutions-creating.md#resources)正在啟動的 Runbook，因為 Runbook 必須建立於作業之前。  您也可針對應在目前 Runbook 之前完成的 Runbook，建立其他作業的相依性。

作業資源的名稱必須包含通常由參數所指派的 GUID。  [在 Operations Management Suite (OMS) 中建立解決方案](operations-management-suite-solutions-creating.md#parameters)可讓您深入了解 GUID 參數。  

以下是在安裝管理解決方案時啟動 Runbook 的作業資源範例。  在此 Runbook 啟動前，必須先完成其他 Runbook，所以它具有該 Runbook 的作業相依性。  作業的詳細資料會透過參數和變數提供，而非直接指定。

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>憑證
[Azure 自動化憑證](../automation/automation-certificates.md)的類型為 **Microsoft.Automation/automationAccounts/certificates**，且具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| base64Value |憑證的 Base 64 值。 |
| thumbprint |憑證的指紋。 |

憑證資源的範例如下。

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>認證
[Azure 自動化認證](../automation/automation-credentials.md)的類型為 **Microsoft.Automation/automationAccounts/credentials**，且具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| userName |認證的使用者名稱。 |
| password |認證的密碼。 |

認證資源的範例如下。

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>排程
[Azure 自動化排程](../automation/automation-schedules.md)的類型為 **Microsoft.Automation/automationAccounts/schedules**，且具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 說明 |排程的選擇性說明。 |
| startTime |將排程的開始時間指定為 DateTime 物件。 如果可以轉換成有效的 DateTime，即可提供字串。 |
| isEnabled |指定是否啟用排程。 |
| interval |排程的間隔類型。<br><br>day<br>hour |
| frequency |應觸發排程的頻率 (以天數或時數為單位)。 |

排程資源的範例如下。

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>變數
[Azure 自動化變數](../automation/automation-variables.md)的類型為 **Microsoft.Automation/automationAccounts/variables**，且具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 說明 |變數的選擇性說明。 |
| isEncrypted |指定是否應加密變數。 |
| 類型 |變數的資料類型。 |
| value |變數的值。 |

變數資源的範例如下。

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>模組
您的管理解決方案不需定義您的 Runbook 所用的[全域模組](../automation/automation-integration-modules.md)，因為永遠可用使用這些模組。  您必須包含 Runbook 所用的任何其他模組的資源，而此 Runbook 應該相依於此模組資源，才能確保它在 Runbook 之前建立。

[整合模組](../automation/automation-integration-modules.md)的類型為 **Microsoft.Automation/automationAccounts/modules**，且具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| contentLink |指定模組的內容。 <br><br>uri - 指定 Runbook 的內容 Uri。  這會是 PowerShell 和 Script Runbook 的 .ps1 檔案，以及 Graph Runbook 的已匯出圖形化 Runbook 檔案。  <br> 版本 - 您自己追蹤的 Runbook 版本。 |

模組資源的範例如下。

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>更新模組
如果您更新的管理解決方案包含使用排程的 Runbook，而且新版的解決方案具有該 Runbook 所用的新模組，則 Runbook 可能會使用舊版的模組。  您應該在您的解決方案中納入下列 Runbook，並建立一項作業，以在任何其他 Runbook 之前執行這些 Runbook。  這可確保在載入 Runbook 之前，會視需要更新任何模組。

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) 可確保您解決方案中 Runbook 使用的所有模組都是最新版本。  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) 可重新註冊所有的排程資源，以確保已連結到這些資源的 Runbook 使用最新模組。

下列是支援模組更新之解決方案的必要元素範例。

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>後續步驟
* [將檢視新增至您的解決方案](operations-management-suite-solutions-resources-views.md)，以將所收集的資料視覺化。




<!--HONumber=Nov16_HO3-->


