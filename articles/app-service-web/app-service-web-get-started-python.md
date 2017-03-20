---
title: "在&5; 分鐘內將您的第一個 Python Web 應用程式建立在 Azure 中 | Microsoft Docs"
description: "藉由部署範例 Python 應用程式，了解在 App Service 中執行 Web 應用程式有多麼簡單。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: dbacf368684bf23f63d997941ac68cf0bc1d9a40
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-python-web-app-in-azure-in-five-minutes"></a>在&5; 分鐘內，將您的第一個 Python Web 應用程式建立在 Azure 中
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

本快速入門可幫助您在短短幾分鐘內將第一個 Python web 應用程式部署至 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

開始本快速入門之前，請確認[安裝 Azure CLI 在](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)您的電腦上。

## <a name="create-a-python-web-app-in-azure"></a>在 Azure 中建立 Python Web 應用程式
2. 執行 `az login` 並遵循螢幕指示登入 Azure。
   
    ```azurecli
    az login
    ```
   
3. 建立[資源群組](../azure-resource-manager/resource-group-overview.md)。 這是您放置要共同管理的所有 Azure 資源之處，例如 web 應用程式和它的 SQL 資料庫後端。

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    若要查看您可用於 `---location` 的可能值，請使用 `az appservice list-locations` Azure CLI 命令。

3. 建立「免費」[App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. 在 `<app_name>` 中使用唯一名稱建立新的 Web 應用程式。

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. 從 GitHub 部署範例 Python 應用程式。

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-python-get-started.git" --branch master --manual-integration 
    ```

5. 若要看見您的應用程式在 Azure 中即時執行，請執行這個命令。

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

恭喜您，第一個 Python web 應用程式在 Azure App Service 中即時執行。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

瀏覽預先建立的 [Web 應用程式 CLI 指令碼](app-service-cli-samples.md)。

