---
title: "Azure PowerShell 指令碼範例 - 使用 FTP 將檔案上傳至 Web 應用程式 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 使用 FTP 將檔案上傳至 Web 應用程式"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 5f3bf8087fa6601ae410d6db907471fd6faa2742
ms.lasthandoff: 03/10/2017

---

# <a name="upload-files-to-a-web-app-using-ftp"></a>使用 FTP 將檔案上傳至 Web 應用程式

此範例指令碼會使用 Azure PowerShell 執行下列工作： 

* 在 Azure 西歐區域的 Azure App Service 中建立 Web 應用程式。
* 從 Web 應用程式取得 FTP 連線資訊
* 使用 FTP 部署 Web 應用程式程式碼 (透過[WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx))。

## <a name="prerequisites"></a>必要條件

* 執行 `Login-AzureRmAccount` 來登入 Azure。

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "使用 FTP 將檔案上傳至 Web 應用程式")]

## <a name="clean-up-deployment"></a>清除部署 

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | 建立 App Service 方案。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | 建立 Web 應用程式。 |
| [Get-AzureRmWebAppPublishingProfile](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/get-azurermwebapppublishingprofile) | 取得 Web 應用程式的發行設定檔。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)。

您可以在 [Azure PowerShell 範例](../app-service-powershell-samples.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。

