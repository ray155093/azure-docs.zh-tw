<properties
    pageTitle="建立服務匯流排命名空間與事件中樞和取用者群組 | Microsoft Azure"
    description="使用 ARM 範本建立服務匯流排命名空間與事件中樞和取用者群組"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# 使用 ARM 範本建立服務匯流排命名空間與事件中樞和取用者群組

本文說明如何使用 Azure Resource Manager (ARM) 範本，建立服務匯流排命名空間與事件中樞和取用者群組。您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求

如需建立範本的詳細資訊，請參閱[編寫 Azure Resource Manager 範本][]。

如需完整的範本，請參閱 GitHub 上的[服務匯流排事件中樞和取用者群組範本][]。

>[AZURE.NOTE] 下列的 ARM 範本可供下載和部署。
>
>-    [建立服務匯流排命名空間與佇列和授權規則](service-bus-arm-namespace-auth-rule.md)
>-    [建立服務匯流排命名空間與佇列](service-bus-arm-namespace-queue.md)
>-    [建立服務匯流排命名空間與主題和訂用帳戶](service-bus-arm-namespace-topic.md)
>-    [建立服務匯流排命名空間](service-bus-arm-namespace.md)
>
>若要檢查最新的範本，請參閱 [Azure 快速入門範本][]並搜尋服務匯流排。

## 您將部署什麼？

使用此範本，您將部署具有事件中樞和取用者群組的服務匯流排命名空間。

事件中樞是事件處理服務，用於提供大規模進入 Azure 的事件和遙測入口，並具備低延遲和高可靠性等特性。

深入了解 [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)。

若要自動執行部署，請按一下下列按鈕：

[![部署至 Azure](./media/service-bus-arm-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-eventhub-and-consumergroup%2Fazuredeploy.json)

## 參數

透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。此範本有一個 `Parameters` 區段，內含所有參數值。您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。請不要為永遠保持不變的值定義參數。每個參數值都可在範本中用來定義所部署的資源。

我們將說明範本中的每個參數。

### serviceBusNamespaceName

要建立的服務匯流排命名空間名稱。

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusEventHubName

在服務匯流排命名空間中建立的事件中樞名稱。

```
" serviceBusEventHubName": {
"type": "string"
}
```

### serviceBusConsumerGroupName

在服務匯流排命名空間中為事件中樞建立的取用者群組名稱。

```
" serviceBusConsumerGroupName": {
"type": "string"
}
```

### serviceBusApiVersion

範本的服務匯流排 API 版本。

```
"serviceBusApiVersion": {
"type": "string"
}
```

## 要部署的資源

建立**事件中樞**類型的服務匯流排命名空間，以及事件中樞和取用者群組。

```
"resources": [
        {
            "apiVersion": "[variables('ehVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/Namespaces",
            "location": "[variables('location')]",
            "kind": "EventHub",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('ehVersion')]",
                    "name": "[parameters('serviceBusEventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusEventHubName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('ehVersion')]",
                            "name": "[parameters('serviceBusConsumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('serviceBusEventHubName')]"
                            ],
                            "properties": {
                            }
                        }
                    ]
                }
            ]
        }
    ]
```

## 執行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-eventhub-and-consumergroup/azuredeploy.json
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-eventhub-and-consumergroup/azuredeploy.json][]
```

## 後續步驟

現在您已使用 ARM 建立並部署資源，請檢視這些文件，了解如何管理這些資源︰

- [使用 Azure 自動化管理 Azure 服務匯流排](service-bus-automation-manage.md)
- [使用 PowerShell 管理事件中樞](service-bus-powershell-how-to-provision.md)
- [使用服務匯流排總管管理事件中樞](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [編寫 Azure Resource Manager 範本]: ../resource-group-authoring-templates.md
  [Azure 快速入門範本]: https://azure.microsoft.com/documentation/templates/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [服務匯流排事件中樞和取用者群組範本]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-eventhub-and-consumerGroup/

<!---HONumber=AcomDC_0420_2016-->