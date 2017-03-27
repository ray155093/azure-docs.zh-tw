---
title: "在&5; 分鐘內將您的第一個 PHP Web 應用程式建立在 Azure 中 | Microsoft Docs"
description: "藉由部署範例 PHP 應用程式，了解在 App Service 中執行 Web 應用程式有多麼簡單。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: afecc8997631bf507c797e56a9e3fc0d1df27614
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-php-web-app-in-azure-in-five-minutes"></a>在&5; 分鐘內，將您的第一個 PHP Web 應用程式建立在 Azure 中
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

本快速入門可幫助您在短短幾分鐘內將第一個 PHP web 應用程式部署至 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

開始之前，請確定已安裝 Azure CLI。 如需詳細資訊，請參閱 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="log-in-to-azure"></a>登入 Azure
執行 `az login` 並遵循螢幕指示登入 Azure。
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>建立資源群組   
建立[資源群組](../azure-resource-manager/resource-group-overview.md)。 這是您放置要共同管理的所有 Azure 資源之處，例如 web 應用程式和它的 SQL 資料庫後端。

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

若要查看您可用於 `---location` 的可能值，請使用 `az appservice list-locations` Azure CLI 命令。

## <a name="create-an-app-service-plan"></a>建立應用程式服務方案
建立執行 Linux 容器的「標準」[App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## <a name="create-a-web-app"></a>建立 Web 應用程式
在 `<app_name>` 中使用唯一名稱建立 Web 應用程式。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="configure-the-linux-container"></a>設定 Linux 容器
設定 Linux 容器以使用預設的 PHP 7.0.6 映像。

```azurecli
az appservice web config update --php-version 7.0.6 --name <app_name> --resource-group myResourceGroup
```
## <a name="deploy-sample-application"></a>部署範例應用程式
從 GitHub 部署範例 PHP 應用程式。

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-php-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>瀏覽至 Web 應用程式
若要看見您的應用程式在 Azure 中即時執行，請執行這個命令。

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

恭喜您，第一個 PHP web 應用程式在 Azure App Service 中即時執行。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

瀏覽預先建立的 [Web 應用程式 CLI 指令碼](app-service-cli-samples.md)。

