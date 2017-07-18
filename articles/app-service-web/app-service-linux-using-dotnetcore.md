---
title: "在 Linux 上的 Azure App Service Web 應用程式中使用 .NET Core | Microsoft Docs"
description: "在 Linux 上的 Azure App Service Web 應用程式中使用 .NET Core。"
keywords: "azure app service, Web 應用程式, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 2d979b954912b8f344aa681980514821058ce5da
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>在 Linux 上的 Azure Web 應用程式中使用 .NET Core #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


在最後更新我們的後端時，我們開始支援 .NET Core 1.0 版。 您可以設定 Linux Web 應用程式的組態，以變更應用程式堆疊。


## <a name="using-the-azure-cli"></a>使用 Azure CLI ##

透過[最新的 Azure 命令列介面 (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli)，您可以使用 **azure webapp config set** 命令來變更應用程式堆疊。 下列是一個範例：

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

**aspnetcore.dll** 檔案是應用程式的 dll。 您在應用程式中可以使用任何您想要的名稱。

這樣會載入 .Net Core 映像並啟動 Web 應用程式。 您可以使用 **azure webapp config show** 檢查設定已正確設定。 下列是一個範例：

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>後續步驟
* [什麼是 Linux 上的 Azure Web 應用程式？](app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 應用程式中建立 Web Apps](./app-service-linux-how-to-create-web-app.md)
* [Azure Web 應用程式跨平台 CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Linux 上的 Azure App Service Web 應用程式常見問題集](app-service-linux-faq.md)
