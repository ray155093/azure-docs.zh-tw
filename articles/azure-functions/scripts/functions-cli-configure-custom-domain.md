---
title: "Azure CLI 指令碼範例 - 將自訂網域對應至函式應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 在 Azure 中將自訂網域對應至函式應用程式。"
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/09/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 60005cb19dd78e9ef7eec02f889b37fd58db0dfd
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>將自訂網域對應至函式應用程式

此範例指令碼會建立函式應用程式及相關資源，然後將 `www.<yourdomain>` 與其對應。 若要對應至自訂網域，必須在 App Service 方案中建立函式應用程式，而不是在取用方案中建立。 Azure Functions 只支援使用 A 記錄對應自訂網域。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "將自訂網域對應至函式應用程式")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | 建立函式應用程式所需的儲存體帳戶。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立對應自訂網域所需的 App Service 方案。 |
| [az functionapp create]() | 建立函式應用程式。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | 將自訂網域對應至函式應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Functions 文件]()中找到其他 Functions CLI 指令碼範例。
