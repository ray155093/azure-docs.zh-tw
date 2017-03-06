---
title: "Azure CLI 指令碼範例 - 建立可從 Visual Studio Team Services 連續部署的 Web 應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立可從 Visual Studio Team Services 連續部署的 Web 應用程式"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: d54e58da5755009710bdcd11eaf845d6ce4f76d4
ms.openlocfilehash: 915227599645186ed11165affd378f2de282c7ea
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>建立可從 Visual Studio Team Services 連續部署的 Web 應用程式

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後設定從 Visual Studio Team Services (VSTS) 存放庫進行的連續部署。 

在執行這個指令碼之前，請確定下列各項︰

- 已使用 `az login` 命令建立與 Azure 的連線。
- 應用程式的程式碼位於您擁有的公用或私人 Visual Studio Team Services 存放庫。
- 您已[在 VSTS 中建立存取權杖](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)。

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="create-app-sample"></a>建立應用程式範例

[!code-azurecli[主要](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "建立可從 Visual Studio Team Services 連續部署的 Web 應用程式")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

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
