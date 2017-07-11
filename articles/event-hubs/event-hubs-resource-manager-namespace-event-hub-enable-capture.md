---
title: "使用範本來建立 Azure 事件中樞命名空間並啟用擷取 | Microsoft Docs"
description: "使用 Azure Resource Manager 範本建立含有一個事件中樞的 Azure 事件中樞命名空間並啟用擷取"
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/28/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f19a3d9b323d75ae23480d0699d55b79bb7d2e84
ms.contentlocale: zh-tw
ms.lasthandoff: 06/28/2017


---
<a id="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template" class="xliff"></a>

# 使用 Azure Resource Manager 範本建立含有一個事件中樞的事件中樞命名空間並啟用擷取
本文說明如何使用 Azure Resource Manager 範本來建立含有一個事件中樞執行個體的事件中樞命名空間，也可在該事件中樞上啟用擷取功能。 此文章說明如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求

如需關於建立範本的詳細資訊，請參閱[編寫 Azure Resource Manager 範本][Authoring Azure Resource Manager templates]。

如需 Azure 資源命名慣例相關模式和實務的詳細資訊，請參閱 [Azure 資源命名慣例][Azure Resources Naming Conventions]。

如需完整的範本，請參閱 GitHub 上的[事件中樞和啟用擷取範本][Event Hub and enable Capture template]。

> [!NOTE]
> 若要檢查最新的範本，請造訪 [Azure 快速入門範本][Azure Quickstart Templates] 資源庫並搜尋事件中樞。
> 
> 

<a id="what-will-you-deploy" class="xliff"></a>

## 您將部署什麼？
使用此範本，您可部署含有事件中樞的事件中樞命名空間，也可啟用[事件中樞擷取](event-hubs-capture-overview.md)。

[事件中樞](event-hubs-what-is-event-hubs.md) 是事件處理服務，用於提供大規模進入 Azure 的事件和遙測入口，並具備低延遲和高可靠性等特性。 事件中樞擷取功能讓您能夠在指定時間或大小間隔內，自動將事件中樞的串流資料傳遞到您選擇的 Azure Blob 儲存體。

若要自動執行部署，請按一下下列按鈕：

[![部署至 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

<a id="parameters" class="xliff"></a>

## 參數
透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。 此範本有一個 `Parameters` 區段，內含所有參數值。 您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。 請不要為永遠保持不變的值定義參數。 每個參數值都可在範本中用來定義所部署的資源。

範本會定義下列參數。

<a id="eventhubnamespacename" class="xliff"></a>

### eventHubNamespaceName
要建立的事件中樞命名空間名稱。

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

<a id="eventhubname" class="xliff"></a>

### eventHubName
在「事件中樞」命名空間中建立的事件中樞名稱。

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

<a id="messageretentionindays" class="xliff"></a>

### messageRetentionInDays
要在事件中樞中保留訊息的天數。 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

<a id="partitioncount" class="xliff"></a>

### partitionCount
要在事件中樞中建立的資料分割數目。

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

<a id="captureenabled" class="xliff"></a>

### captureEnabled
在事件中樞上啟用封存擷取功能。

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
<a id="captureencodingformat" class="xliff"></a>

### captureEncodingFormat
您指定用來將事件資料序列化的編碼格式。

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

<a id="capturetime" class="xliff"></a>

### captureTime
事件中樞擷取功能開始擷取 Azure Blob 儲存體資料的時間間隔。

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the capture"
    }
}
```

<a id="capturesize" class="xliff"></a>

### captureSize
擷取功能開始擷取 Azure Blob 儲存體資料的大小間隔。

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

<a id="destinationstorageaccountresourceid" class="xliff"></a>

### destinationStorageAccountResourceId
擷取功能需要有 Azure 儲存體帳戶資源識別碼，才能為您所需的儲存體帳戶啟用擷取功能。

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource id where you want the blobs be captured"
    }
 }
```

<a id="blobcontainername" class="xliff"></a>

### blobContainerName
用來擷取存事件資料的 blob 容器。

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```


<a id="apiversion" class="xliff"></a>

### apiVersion
範本的 API 版本。

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

<a id="resources-to-deploy" class="xliff"></a>

## 要部署的資源
建立類型為 **EventHubs**、含有一個事件中樞的命名空間，同時啟用擷取功能。

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
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
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "CaptureDescription":{
                        "enabled":"[parameters('captureEnabled')]",
                        "encoding":"[parameters('captureEncodingFormat')]",
                        "intervalInSeconds":"[parameters('captureTime')]",
                        "sizeLimitInBytes":"[parameters('captureSize')]",
                        "destination":{
                            "name":"EventHubCapture.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

<a id="commands-to-run-deployment" class="xliff"></a>

## 執行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

<a id="powershell" class="xliff"></a>

## PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

<a id="azure-cli" class="xliff"></a>

## Azure CLI
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```
<a id="next-steps" class="xliff"></a>

## 後續步驟

您也可以透過 [Azure 入口網站](https://portal.azure.com)設定事件中樞擷取功能。 如需詳細資訊，請參閱[使用 Azure 入口網站啟用事件中樞擷取功能](event-hubs-capture-enable-through-portal.md)。

您可以造訪下列連結以深入了解事件中樞︰

* [事件中樞概觀](event-hubs-what-is-event-hubs.md)
* [建立事件中樞](event-hubs-create.md)
* [事件中樞常見問題集](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture

