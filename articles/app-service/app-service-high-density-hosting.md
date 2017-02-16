---
title: "Azure App Service 上的高密度裝載 | Microsoft Docs"
description: "Azure App Service 上的高密度託管"
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/11/2017
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0c2677b388f7a88ff88715a05212633565393cc2
ms.openlocfilehash: 2d5d1d5123ca718b2e7dcdf426b77f91969dc9dc


---
# <a name="high-density-hosting-on-azure-app-service"></a>Azure App Service 上的高密度託管
在使用 App Service 時，應用程式會經由兩種概念與其所配置的容量分離：

* **應用程式︰** 代表應用程式和其執行階段組態。 例如，它包含執行階段應載入的 .NET 版本、應用程式設定等等。
* **App Service 方案︰** 定義容量、可用功能集和應用程式位置的特性。 例如，特性可能是大型 (四個核心) 機器、四個執行個體、美國東部的進階功能。

應用程式一律會連結至 App Service 方案，但 App Service 方案可以提供容量給一或多個應用程式。

因此，平台可提供隔離單一應用程式的彈性，或透過共用 App Service 方案而讓多個應用程式共用資源。

不過，當多個應用程式共用 App Service 方案時，該 App Service 方案的每個執行個體上便會執行該應用程式的執行個體。

## <a name="per-app-scaling"></a>每一應用程式調整
*每一應用程式調整* 是可在 App Service 方案層級啟用，然後以每一應用程式為基礎來使用的功能。

每一應用程式調整會獨立縮放應用程式，不受裝載它的 App Service 方案所影響。 如此一來，App Service 方案便可設定為提供 10 個執行個體，但應用程式可以設定為調整到只有其中 5 個。

   >[!NOTE]
   >只有「進階」SKU App Service 方案才能使用個別應用程式調整
   >

### <a name="per-app-scaling-using-powershell"></a>使用 PowerShell 進行個別應用程式調整

您可以透過將 ```-perSiteScaling $true``` 屬性傳遞給 ```New-AzureRmAppServicePlan``` Commandlet，建立一個設定為「個別應用程式調整」方案的新方案

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

如果您想要將現有的 App Service 方案更新成使用此功能： 

- 取得目標方案 ```Get-AzureRmAppServicePlan```
- 在本機修改屬性 ```$newASP.PerSiteScaling = $true```
- 將您的變更張貼回 Azure ```Set-AzureRmAppServicePlan``` 

```
    # Get the new App Service Plan and modify the "PerSiteScaling" property.
    $newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
    $newASP

    #Modify the local copy to use "PerSiteScaling" property.
    $newASP.PerSiteScaling = $true
    $newASP
    
    #Post updated app service plan back to azure
    Set-AzureRmAppServicePlan $newASP
```

擁有已設定的方案之後，您可以設定每個應用程式的執行個體數目上限。

在以下範例中，應用程式的上限為&2; 個執行個體，不論其基礎 App Service 方案的規模相應放大到多少個執行個體。

```
    # Get the app we want to configure to use "PerSiteScaling"
    $newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
    # Modify the NumberOfWorkers setting to the desired value.
    $newapp.SiteConfig.NumberOfWorkers = 2
    
    # Post updated app back to azure
    Set-AzureRmWebApp $newapp
```

### <a name="per-app-scaling-using-azure-resource-manager"></a>使用 Azure Resource Manager 進行個別應用程式調整

下列「Azure Resource Manager 範本」會建立：

- 規模相應放大到 10 個執行個體的 App Service 方案
- 已設定為將上限調整成&5; 個執行個體的應用程式。

App Service 方案會將 **PerSiteScaling** 屬性設為 true (```"perSiteScaling": true```)。 應用程式會將要使用的「背景工作角色數目」設定為 5 (```"properties": { "numberOfWorkers": "5" }```)。

```
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
                "name": "P1",
                "tier": "Premium",
                "size": "P1",
                "family": "P",
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
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }
```

## <a name="recommended-configuration-for-high-density-hosting"></a>高密度裝載的建議組態
每一應用程式調整是公用 Azure 區域和 App Service 環境中啟用的功能。 不過，建議策略是使用 App Service 環境，以利用其進階功能和較大的容量集區。  

請遵循下列步驟來設定應用程式的高密度裝載：

1. 設定 App Service 環境，並選擇專用於高密度裝載案例的背景工作角色集區。
2. 建立單一 App Service 方案，並將其調整為使用背景工作角色集區上所有可用的容量。
3. 在 App Service 方案上將 PerSiteScaling 旗標設定為 true。
4. 新應用程式會建立並指派給該 App Service 方案，其中 **numberOfWorkers** 屬性會設定為 **1**。 使用此設定會產生此背景工作角色集區上所能允許的最高密度。
5. 背景工作角色數目可依每個應用程式單獨設定，以視需要授與額外資源。 例如，高使用率應用程式可以將 **numberOfWorkers** 設定為 **3**，讓該應用程式擁有更多處理容量，而低使用率應用程式則可將 **numberOfWorkers** 設定為 **1**。




<!--HONumber=Jan17_HO2-->


