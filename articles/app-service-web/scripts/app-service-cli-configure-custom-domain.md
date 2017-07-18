---
title: "Azure CLI 指令碼範例 - 將自訂網域對應至 Web 應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 將自訂網域對應至 Web 應用程式"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
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
ms.openlocfilehash: 6712be8a551731fbafd92ef19564e89399e23e76
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---
# <a name="map-a-custom-domain-to-a-web-app"></a>將自訂網域對應至 Web 應用程式

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後將 `www.<yourdomain>` 與其對應。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[主要](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "將自訂網域對應至 Web 應用程式")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) | 建立 Azure Web 應用程式。 |
| [az webapp config hostname add](https://docs.microsoft.com/cli/azure/webapp/config/hostname#add) | 將自訂網域對應至 Web 應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。

