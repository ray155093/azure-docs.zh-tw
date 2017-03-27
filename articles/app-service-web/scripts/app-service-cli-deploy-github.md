---
title: "Azure CLI 指令碼範例 - 建立可從 GitHub 部署的 Web 應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立可從 GitHub 部署的 Web 應用程式"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: ab4be9d9a03001789bed90e58d62fe1891608823
ms.lasthandoff: 03/11/2017

---


# <a name="create-a-web-app-with-deployment-from-github"></a>建立可從 GitHub 部署的 Web 應用程式

此範例指令碼會使用 Azure CLI 2.0 執行下列工作：

* 在 Azure 西歐區域的 Azure App Service 中建立 Web 應用程式。
* 從 GitHub 部署 Web 應用程式程式碼。
* 在瀏覽器中顯示已部署的 Azure Web 應用程式。

## <a name="prerequisites"></a>必要條件

* 執行 `az login` 來登入 Azure。
* 將 Web 應用程式程式碼放置在 GitHub 存放庫。

> [!NOTE]
> 如果您使用非自有的公用 GitHub 存放庫，App Service 會從該 GitHub 存放庫部署程式碼，但無法設定連續部署所需的 SSH 金鑰和 Webhook。
>
>

## <a name="create-app-sample"></a>建立應用程式範例

[!code-azurecli[主要](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "建立可從 GitHub 部署的 Web 應用程式")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 建立 Azure Web 應用程式。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | 將 Azure Web 應用程式關聯到 Git 或 Mercurial 存放庫。 |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 在瀏覽器中開啟 Azure Web 應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。
