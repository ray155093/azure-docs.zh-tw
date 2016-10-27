<properties
    pageTitle="使用 Azure Resource Manager 範本建立事件中樞命名空間與事件中樞和取用者群組 | Microsoft Azure"
    description="使用 Azure Resource Manager 範本建立事件中樞命名空間與事件中樞和取用者群組"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>


# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立事件中樞命名空間與事件中樞和取用者群組

本文說明如何使用 Azure Resource Manager 範本，建立事件中樞命名空間與事件中樞和取用者群組。 您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求

如需建立範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本][]。

如需完整的範本，請參閱 GitHub 上的 [事件中樞和取用者群組範本][] 。

>[AZURE.NOTE]
>若要檢查最新的範本，請造訪 [Azure 快速入門範本][] 資源庫並搜尋事件中樞。

## <a name="what-will-you-deploy?"></a>您將部署什麼？

您將使用此範本，部署具有事件中樞和取用者群組的事件中樞命名空間。

[事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md) 是事件處理服務，用於提供大規模進入 Azure 的事件和遙測入口，並具備低延遲和高可靠性等特性。

若要自動執行部署，請按一下下列按鈕：

[![部署至 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>參數

透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。 此範本有一個 `Parameters` 區段，內含所有參數值。 您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。 請不要為永遠保持不變的值定義參數。 每個參數值都可在範本中用來定義所部署的資源。

範本會定義下列參數。

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

要建立的事件中樞命名空間名稱。

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

在事件中樞命名空間中建立的事件中樞名稱。

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

為事件中樞建立的取用者群組名稱。

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

範本的 API 版本。

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>要部署的資源

建立 **EventHubs**類型的服務匯流排命名空間，以及事件中樞和取用者群組。

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>執行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[編寫 Azure Resource Manager 範本]: ../resource-group-authoring-templates.md
[Azure 快速入門範本]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[搭配使用 Azure PowerShell 與 Azure Resource Manager]: ../powershell-azure-resource-manager.md
[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理]: ../xplat-cli-azure-resource-manager.md
[事件中樞和取用者群組範本]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/



<!--HONumber=Oct16_HO2-->


