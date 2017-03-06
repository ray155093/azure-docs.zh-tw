---
title: "Azure CLI 指令碼範例 - 將 Web 應用程式連接至儲存體帳戶 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 將 Web 應用程式連接至儲存體帳戶"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: c23bab16ee8bf771f4af44abc94e958f512d5390
ms.openlocfilehash: 6ea4c0f82bf29b5683a851da9e6c1134fe0394af
ms.lasthandoff: 02/27/2017

---

# <a name="connect-a-web-app-to-a-storage-account"></a>將 Web 應用程式連接至儲存體帳戶

在此案例中，您會學習如何建立 Azure 儲存體帳戶和 Azure Web 應用程式。 然後，您會使用應用程式設定將儲存體帳戶連結到 Web 應用程式。

在執行這個指令碼之前，請確定您已使用 `az login` 命令建立與 Azure 的連線。

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure 儲存體")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、App Service 應用程式和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、Web 應用程式、儲存體帳戶和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 這就像是 Azure Web 應用程式的伺服器陣列。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | 在 App Service 方案內建立 Azure Web 應用程式。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | 建立儲存體帳戶。 這是靜態資產的儲存位置。 |
| [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account#show-connection-string) | |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | 建立或更新 Azure Web 應用程式的應用程式設定。 應用程式設定會顯示為應用程式的環境變數。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。
