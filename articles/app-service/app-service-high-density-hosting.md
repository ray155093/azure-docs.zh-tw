<properties 
	pageTitle="Azure App Service 上的高密度裝載" 
	description="Azure App Service 上的高密度裝載" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/07/2016" 
	ms.author="byvinyal"/>

# Azure App Service 上的高密度裝載#

## 了解應用程式調整##

在使用 App Service 時，應用程式會經由 2 種概念與其所配置的容量分離︰
 
>**應用程式︰**代表應用程式和其執行階段組態。例如，「執行階段應載入哪個版本的 .NET？」、「應用程式設定為何？」等等。

>**App Service 方案︰**定義容量、可用功能集和應用程式位置的特性。例如，大型 (4 核心) 電腦、4 個執行個體、高階功能、在美國東部

應用程式一律會連結至 **App Service 方案**，但 **App Service 方案**可以提供容量給一或多個應用程式。

其所代表的意義是，平台可提供隔離單一應用程式的彈性，或透過共用 **App Service 方案**而讓多個應用程式共用資源。

不過，當多個應用程式共用 **App Service 方案**時，該 **App Service 方案**的每個執行個體上便會執行該應用程式的執行個體。

## 每一應用程式調整##
**每一應用程式調整**是可在 **App Service 方案**層級啟用，然後以每一應用程式為基礎來利用的功能。

**每一應用程式調整**可讓您單獨調整應用程式，而不受用來裝載它的 **App Service 方案**所限制。如此一來，**App Service 方案**便可設定為提供 10 個執行個體，但應用程式可以設定為調整到只有其中 5 個。

下面的 ARM 範本會建立相應放大為 10 個執行個體的 **App Service 方案**，以及設定為使用**每一應用程式調整**，但只調整為 5 個執行個體的應用程式。

為了這樣做，App Service 方案會將**每一網站調整**屬性設定為 true ( `"perSiteScaling": true`)，而且應用程式會將**背景工作角色數目**設定為使用 1 `"properties": { "numberOfWorkers": "1" }`

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true 
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "1" }
             } ]
         }]
    }


## 高密度裝載的建議組態

**每一應用程式調整**是會同時在公用 Azure 區域以及 App Service 環境啟用的功能，但建議策略是使用 App Service 環境來利用其進階功能和較大的容量集區。

請遵循下列步驟，以做為如何設定應用程式的**高密度裝載**的方針。

1. 設定 **App Service 環境**，並選擇專用於「高密度裝載」案例的**背景工作角色集區**。

1. 建立單一 **App Service 方案**，並將其調整為使用**背景工作角色集區**上所有可用的容量。

1. 在 **App Service 方案**上將每一網站調整旗標設定為 true。

1. 新網站便會建立並指派給該 **App Service 方案**，其中「numberOfWorkers」屬性會設定為「1」。這會產生此**背景工作角色集區**上所能允許的最高密度

1. 背景工作角色數目可以每個網站單獨設定，以視需要授與其他資源。例如，高使用率網站可以將「numberOfWorkers」設定為「3」，讓該應用程式擁有更多處理容量，而低使用率網站則可將「numberOfWorkers」設定為「1」。

<!---HONumber=AcomDC_0810_2016-->