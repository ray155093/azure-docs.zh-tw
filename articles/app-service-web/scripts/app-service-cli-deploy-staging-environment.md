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
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 15a811d35aab9995f4895a9106771310a95f7a41
ms.openlocfilehash: 6ef9b43fb5fb88591186b20b52dd8f06b7d46cec
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>建立 Web 應用程式並將程式碼部署至預備環境

此範例指令碼會在 App Service 中建立 Web 應用程式以及稱為「預備」的其他部署位置，然後將範例應用程式部署至「預備」位置。

在執行這個指令碼之前，請確定您已使用 `az login` 命令建立與 Azure 的連線。 

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "建立 Web 應用程式並將程式碼部署至預備環境")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、App Service 應用程式和所有相關資源。

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
| [az appservice web deployment slot create](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#create) | 建立部署位置。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | 將 Azure Web 應用程式關聯到 Git 或 Mercurial 存放庫。 |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 在瀏覽器中開啟 Azure Web 應用程式。 |
| [az appservice web deployment slot swap](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#swap) | 將指定的部署位置切換到生產環境。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。
