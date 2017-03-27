---
title: "Azure PowerShell 指令碼範例 - 建立 Web 應用程式並從 GitHub 部署程式碼 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 建立 Web 應用程式並從 GitHub 部署程式碼"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 7ee385060bbb3800e8a37f2ae4ba7d06fb31b521
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-web-app-and-deploy-code-from-github"></a>建立 Web 應用程式並從 GitHub 部署程式碼

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後從公用 GitHub 存放庫部署 Web 應用程式程式碼 (不使用連續部署)。 如需進行使用連續部署的 GitHub 部署，請參閱[建立可從 GitHub 連續部署的 Web 應用程式](app-service-powershell-continuous-deployment-github.md)。

在執行這個指令碼之前，請確定您已使用 `Login-AzureRmAccount` Cmdlet 建立與 Azure 的連線，並且擁有要部署的 GitHub 存放庫 URL。

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/app-service/deploy-github/deploy-github.ps1?highlight=1-2 "建立 Web 應用程式並從 GitHub 部署程式碼")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | 建立 App Service 方案。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | 建立 Web 應用程式。 |
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/set-azurermresource) | 修改資源群組中的資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)。

您可以在 [Azure PowerShell 範例](../app-service-powershell-samples.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。

