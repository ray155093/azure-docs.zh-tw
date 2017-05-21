---
title: "Azure CLI 指令碼範例 - 建立 Web 應用程式並將程式碼部署至預備環境 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立 Web 應用程式並將程式碼部署至預備環境"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 6ee3008ab00fcdca3d1eb8330237bed196fad813
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>建立 Web 應用程式並將程式碼部署至預備環境

此範例指令碼會在 App Service 中建立 Web 應用程式以及稱為「預備」的其他部署位置，然後將範例應用程式部署至「預備」位置。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[主要](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "建立 Web 應用程式並將程式碼部署至預備環境")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 建立 Azure Web 應用程式。 |
| [az appservice web deployment slot create](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#create) | 建立部署位置。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | 將 Azure Web 應用程式關聯到 Git 或 Mercurial 存放庫。 |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 在瀏覽器中開啟 Azure Web 應用程式。 |
| [az appservice web deployment slot swap](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#swap) | 將指定的部署位置切換到生產環境。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。

