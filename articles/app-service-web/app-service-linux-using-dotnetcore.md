---
title: "在 Linux 上的 Azure App Service Web Apps 中使用 .NET Core | Microsoft Docs"
description: "在 Linux 上的 Azure App Service Web Apps 中使用 .NET Core。"
keywords: "azure app service, Web 應用程式, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: a54b7413c2eb6ae67b7424a8966d0cd82cd1ce6a


---

# <a name="using-net-core-in-web-apps-on-linux"></a>在 Linux 上的 Web Apps 中使用 .NET Core #

在最後更新我們的後端時，我們開始支援 .NET Core 1.0 版。 您可以設定 Linux Web 應用程式的組態，以變更應用程式堆疊。


## <a name="using-xplat-cli"></a>使用 XPlat CLI ##

透過最新的 Azure 跨平台 CLI，您可以使用 **azure webapp config set** 命令來變更應用程式堆疊。 範例如下：

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

請注意，**aspnetcore.dll** 檔案是應用程式的 dll。 您在應用程式中可以使用任何您想要的名稱。

這樣會載入 .Net Core 映像並啟動 Web 應用程式。 您可以使用 **azure webapp config show** 檢查設定已正確設定。 範例如下：

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>後續步驟
* [Linux 上的 App Service 簡介](./app-service-linux-intro.md) 
* [何謂 Linux 上的 App Service？](app-service-linux-intro.md)
* [在 Linux 上的 App Service 中建立 Web Apps](./app-service-linux-how-to-create-a-web-app.md)
* [Azure Web 應用程式跨平台 CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)



<!--HONumber=Nov16_HO3-->


