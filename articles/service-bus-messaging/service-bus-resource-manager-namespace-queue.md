---
title: 使用 Azure Resource Manager 範本建立服務匯流排命名空間和佇列 | Microsoft Docs
description: 使用 Azure Resource Manager 範本建立服務匯流排命名空間和佇列
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 07/11/2016
ms.author: sethm;shvija

---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立服務匯流排命名空間和佇列
本文說明如何使用 Azure Resource Manager 範本，建立服務匯流排命名空間和佇列。 您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。

如需建立範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本][編寫 Azure Resource Manager 範本]。

如需完整的範本，請參閱 GitHub 上的 [服務匯流排命名空間和佇列範本][服務匯流排命名空間和佇列範本] 。

> [!NOTE]
> 下列 Azure Resource Manager 範本可供下載和部署。
> 
> * [建立服務匯流排命名空間與佇列和授權規則](service-bus-resource-manager-namespace-auth-rule.md)
> * [建立服務匯流排命名空間與主題和訂用帳戶](service-bus-resource-manager-namespace-topic.md)
> * [建立服務匯流排命名空間](service-bus-resource-manager-namespace.md)
> * [建立事件中樞命名空間與事件中樞和取用者群組](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
> 
> 若要檢查最新的範本，請造訪 [Azure 快速入門範本][Azure 快速入門範本] 資源庫並搜尋服務匯流排。
> 
> 

## <a name="what-will-you-deploy?"></a>您將部署什麼？
使用此範本，您將部署具有佇列的服務匯流排命名空間。

如果有一或多個競爭取用者，[服務匯流排佇列](service-bus-queues-topics-subscriptions.md#queues)會採用「先進先出」(FIFO) 訊息傳遞機制。

若要自動執行部署，請按一下下列按鈕：

[![部署至 Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>參數
透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。 此範本有一個 `Parameters` 區段，內含所有參數值。 您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。 請不要為永遠保持不變的值定義參數。 每個參數值都可在範本中用來定義所部署的資源。

範本會定義下列參數。

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
要建立的服務匯流排命名空間名稱。

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
在服務匯流排命名空間中建立的佇列名稱。

```
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
範本的服務匯流排 API 版本。

```
"serviceBusApiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>要部署的資源
建立 **訊息**類型的標準服務匯流排命名空間和佇列。

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>執行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>後續步驟
現在您已使用 Azure Resource Manager 建立並部署資源，請檢視這些文件，了解如何管理這些資源︰

* [使用 PowerShell 管理服務匯流排](../service-bus/service-bus-powershell-how-to-provision.md)
* [使用服務匯流排總管管理服務匯流排資源](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[編寫 Azure Resource Manager 範本]: ../resource-group-authoring-templates.md
[服務匯流排命名空間和佇列範本]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure 快速入門範本]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[深入了解服務匯流排佇列]: service-bus-queues-topics-subscriptions.md
[搭配使用 Azure PowerShell 與 Azure Resource Manager]: ../powershell-azure-resource-manager.md
[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理]: ../xplat-cli-azure-resource-manager.md



<!--HONumber=Oct16_HO2-->


