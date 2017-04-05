---
title: "Azure CLI 指令碼範例 - 在 Batch 中加入應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 在 Batch 中加入應用程式"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 342cc2c93304f7a8f651769139e28ebf2e30d412
ms.lasthandoff: 03/24/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>使用 Azure CLI 將應用程式加入 Azure Batch

此指令碼示範如何設定應用程式，以搭配 Azure Batch 集區或作業使用。 設定應用程式，將可執行檔及任何相依性封裝到 .zip 檔案。 在此範例中，可執行的 zip 檔案稱為 'my-application-exe.zip'。
執行這個指令碼的前提是您已設定 Batch 帳戶。 如需詳細資訊，請參閱[建立 Batch 帳戶的範例指令碼](./batch-cli-sample-create-account.md)。

您可以視需要使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的指示來安裝 Azure CLI，然後執行 `az login` 以登入 Azure。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/batch/add-application/add-application.sh "加入應用程式")]

## <a name="clean-up-application"></a>清除應用程式

執行上述範例指令碼之後，請執行下列命令以移除應用程式和其所有已上傳的應用程式封裝。

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>指令碼說明

這個指令碼使用下列命令來建立應用程式和上傳應用程式封裝。
下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | 建立應用程式。  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | 更新應用程式的屬性。  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | 將應用程式封裝加入指定的應用程式。  |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Batch CLI 文件](../batch-cli-samples.md)中找到其他的 Batch CLI 指令碼範例。

