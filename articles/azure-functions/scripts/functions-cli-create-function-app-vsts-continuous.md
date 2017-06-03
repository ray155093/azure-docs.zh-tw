---
title: "建立函式應用程式和從 Visual Studio Team Services 部署函式程式碼 | Microsoft Docs"
description: "建立函式應用程式和從 Visual Studio Team Services 部署函式程式碼"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/28/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 8e5d8bdf61746d3bda5acc7bed97b164c311a3c3
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017

---
# <a name="create-an-app-service"></a>建立 App Service

在此案例中，您將學習如何使用[取用方案](../functions-scale.md#consumption-plan)建立函式應用程式及其相關資源，然後從 Visual Studio Team Services (VSTS) 存放庫持續部署您的函式程式碼。 在此範例中，您將需要：

* 一個含有函式程式碼的 VSTS 存放庫 (您必須有此存放庫的系統管理權限)。
* 您 GitHub 帳戶的[個人存取權杖 (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

此指令碼範例會建立 Azure 函數應用程式，並從 Visual Studio Team Services 部署函式程式碼。

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure 服務")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、Web 應用程式、documentdb 和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | 將函式應用程式關聯到 Git 或 Mercurial 存放庫。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Functions 文件](../functions-cli-samples.md)中找到其他 Azure Functions CLI 指令碼範例。

