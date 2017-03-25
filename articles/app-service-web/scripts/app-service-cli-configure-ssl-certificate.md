---
title: "Azure CLI 指令碼範例 - 將自訂 SSL 憑證繫結至 Web 應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 將自訂 SSL 憑證繫結至 Web 應用程式"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: a28bb7b8d30ebe6589dfb792e9294a180544569f
ms.lasthandoff: 03/11/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>將自訂 SSL 憑證繫結至 Web 應用程式

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關的資源，然後將自訂網域名稱的 SSL 憑證加以繫結。 

在執行這個指令碼之前，請確定︰

- 已使用 `az login` 命令建立與 Azure 的連線。
- 您可以存取網域註冊機構的 DNS 設定頁面。
- 對於想要上傳並繫結的 SSL 憑證，您具備有效的 .PFX 檔案和其密碼。

這個範例適用於 Bash 殼層。 如需在 Windows 用戶端上執行 Azure CLI 指令碼的選項，請參閱[在 Windows 中執行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[主要](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "將自訂 SSL 憑證繫結至 Web 應用程式")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立 App Service 方案。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 建立 Azure Web 應用程式。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | 更新 App Service 方案來調整其定價層。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | 將 SSL 憑證上傳至 Web 應用程式。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | 將上傳的 SSL 憑證繫結至 Web 應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。
