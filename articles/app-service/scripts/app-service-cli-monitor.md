---
title: "Azure CLI 指令碼範例 - 使用 Web 伺服器記錄監視 Web 應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 使用 Web 伺服器記錄監視 Web 應用程式"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: c23bab16ee8bf771f4af44abc94e958f512d5390
ms.openlocfilehash: 82a823853497282681b4b724655ba67d6ed7e9eb
ms.lasthandoff: 02/27/2017

---

# <a name="monitor-a-web-app-with-web-server-logs"></a>使用 Web 伺服器記錄監視 Web 應用程式

在此案例中，您會建立資源群組、App Service 方案、Web 應用程式，並設定 Web 應用程式來啟用 Web 伺服器記錄。 然後，您會下載記錄檔來進行檢閱。

在執行這個指令碼之前，請確定您已使用 `az login` 命令建立與 Azure 的連線。

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "監視記錄")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、App Service 應用程式和所有相關資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、Web 應用程式和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 這就像是 Azure Web 應用程式的伺服器陣列。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | 在 App Service 方案內建立 Azure Web 應用程式。 |
| [az appservice web log config](https://docs.microsoft.com/cli/azure/appservice/web/log#config) | 設定 Azure Web 應用程式會保存的記錄。 |
| [az appservice web log download](https://docs.microsoft.com/cli/azure/appservice/web/log#download) | 將 Azure Web 應用程式的記錄下載到本機電腦。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。
