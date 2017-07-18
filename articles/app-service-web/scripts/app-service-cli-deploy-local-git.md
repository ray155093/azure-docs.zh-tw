---
title: "Azure CLI 指令碼範例 - 建立 Web 應用程式並從本機 Git 存放庫部署程式碼 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立 Web 應用程式並從本機 Git 存放庫部署程式碼"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 50d69ac48438920ce59808ee79809235d8330b14
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---

# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>建立 Web 應用程式並從本機 Git 存放庫部署程式碼

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後在本機 Git 存放庫中部署 Web 應用程式程式碼。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[主要](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "建立 Web 應用程式並從本機 Git 存放庫部署程式碼")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) | 建立 Azure Web 應用程式。 |
| [az webapp deployment user set](https://review.docs.microsoft.com/cli/azure/webapp/deployment/user#set) | 設定 App Service 的帳戶層級部署認證。 |
| [az webapp deployment source config-local-git](https://review.docs.microsoft.com/cli/azure/webapp/deployment/source#config-local-git) | 建立本機 Git 存放庫的原始檔控制組態。 |
| [az webapp browse](https://docs.microsoft.com/cli/azure/webapp#browse) | 在瀏覽器中開啟 Azure Web 應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。

