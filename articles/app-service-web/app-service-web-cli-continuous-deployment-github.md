---
title: "Azure CLI 指令碼範例 - 從 GitHub 連續部署 Web 應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 從 GitHub 連續部署 Web 應用程式"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: d19b07667bc2d34f860be46c299560fe9a788d53
ms.openlocfilehash: 649521d6f3740b099ab41c8ef70f7e4430aa5de9
ms.lasthandoff: 02/27/2017

---

# <a name="continuously-deploy-web-app-from-github"></a>從 GitHub 連續部署 Web 應用程式

此範例指令碼會使用 Azure CLI 2.0 執行下列工作： 

* 在 Azure 西歐區域的 Azure App Service 中建立 Web 應用程式。 
* 從 GitHub 部署 Web 應用程式程式碼。
* 在瀏覽器中顯示已部署的 Web 應用程式。

## <a name="prerequisites"></a>必要條件

* 執行 `az login` 來登入 Azure。
* 將 Web 應用程式程式碼放置在您擁有的 GitHub 存放庫。

> [!NOTE]
> 如果您使用非自有的公用 GitHub 存放庫，App Service 會從該 GitHub 存放庫部署程式碼，但無法設定連續部署所需的 SSH 金鑰和 Webhook。
>
>

## <a name="create-vm-sample"></a>建立 VM 範例

[!code-azurecli[主要](../../cli_scripts/app-service/deploy-github/deploy-github.sh "從 GitHub 連續部署 Web 應用程式")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | 建立 App Service 方案。 |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | 建立 Web 應用程式。 |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | 將 Web 應用程式關聯到 Git 或 Mercurial 存放庫。 |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | 在瀏覽器中開啟 Web 應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/en-us/cli/azure/overview)。

您可以在 [Azure CLI 範例]()中找到適用於 Azure App Service Web Apps 的其他 CLI 指令碼範例。

