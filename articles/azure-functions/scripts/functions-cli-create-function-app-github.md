---
title: "建立函式應用程式和從 GitHub 部署函式程式碼 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立函式應用程式和從 GitHub 部署函式程式碼"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: d67e85f91c80efe464fceb1105243bedfba83a0f
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>建立函式應用程式和從 GitHub 部署函式程式碼

此範例指令碼會使用[取用方案](../functions-scale.md#consumption-plan)建立函式應用程式及其相關資源，然後從公用 GitHub 存放庫部署您的函式程式碼 (沒有持續部署)。 若要從 GitHub 持續傳遞函式程式碼，請參閱[建立函式應用程式和從 GitHub 持續部署](functions-cli-create-function-app-github-continuous.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

此指令碼範例會建立 Azure 函式應用程式，並從 GitHub 部署函式程式碼。

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "建立可從 GitHub 部署的函式應用程式")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

下表中的每個命令都會連結至命令特定的文件。 此指令碼會使用下列命令：

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 建立 Azure 函式應用程式。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | 將函式應用程式關聯到 Git 或 Mercurial 存放庫。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Functions 文件](../functions-cli-samples.md)中找到其他 Azure Functions CLI 指令碼範例。

