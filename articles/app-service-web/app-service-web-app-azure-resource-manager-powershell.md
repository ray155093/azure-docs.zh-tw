---
title: "適用於 Azure Web 應用程式的 Azure Resource Manager 架構 PowerShell 命令 | Microsoft Docs"
description: "深入了解如何使用新的 Azure Resource Manager 架構 PowerShell 命令來管理 Azure Web Apps"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 4231fbba-61e5-4f92-b958-531c066fb87f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8d574f051a327ba0409e6f25a5886af673d3d5e0
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017


---
# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>使用 Azure Resource Manager 架構 PowerShell 來管理 Azure Web Apps
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Microsoft Azure PowerShell 1.0.0 版已加入新的命令，讓使用者能夠使用 Azure Resource Manager 架構 PowerShell 命令來管理 Web Apps。

若要深入了解如何管理資源群組，請參閱 [搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。 

若要深入了解適用於 PowerShell Cmdlet 的完整參數和選項清單，請參閱 [Web App Azure Resource Manager 架構 PowerShell Cmdlet 的完整 Cmdlet 參考](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>管理 App Service 方案
### <a name="create-an-app-service-plan"></a>建立 App Service 方案
若要建立 App Service 方案，請使用 **New-AzureRmAppServicePlan** Cmdlet。

以下是不同參數的說明︰

* **Name**：App Service 方案的名稱。
* **Location**：服務方案名稱。
* **ResourceGroupName**：包含新建立的 App Service 方案的資源群組。
* **Tier**︰想要的定價層 (預設值是 [免費]，其他選項包括 [共用]、[基本]、[標準] 和 [進階])。
* **WorkerSize**︰背景工作大小 (如果 Tier 參數指定為 [基本]、[標準] 或 [進階]，則預設值為 [小型]。 其他選項為 [中型] 和 [大型])。
* **NumberofWorkers**︰App Service 方案中的背景工作數目 (預設值為 1)。 

使用此 Cmdlet 的範例︰

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>在 App Service 環境中建立 App Service 方案
若要在 App Service 環境中建立 App Service 方案，可以使用相同的 **New-AzureRmAppServicePlan** 命令搭配額外的參數，來指定 ASE 的名稱和 ASE 的資源群組名稱。

使用此 Cmdlet 的範例︰

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

若要深入了解 App Service 環境，請查看 [App Service 環境簡介](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>列出現有的 App Service 方案
若要列出現有的 App Service 方案，請使用 **Get-AzureRmAppServicePlan** Cmdlet。

若要列出您訂用帳戶下方的所有 App Service 方案，請使用： 

    Get-AzureRmAppServicePlan

若要列出特定資源群組之下的所有 App Service 方案，請使用︰

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

若要取得特定的 App Service 方案，請使用︰

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>設定現有的 App Service 方案
若要變更現有 App Service 方案的設定，請使用 **Set-AzureRmAppServicePlan** Cmdlet。 您可以變更層級、背景工作大小和背景工作數目 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>調整 App Service 方案
若要調整現有的 App Service 方案，請使用：

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>變更 App Service 方案的背景工作大小
若要變更現有 App Service 方案中的背景工作大小，請使用︰

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>變更 App Service 方案的層級
若要變更現有 App Service 方案的層級，請使用︰

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>刪除現有的 App Service 方案
若要刪除現有的 App Service 方案，需要先移動或刪除所有已指派的 Web 應用程式。 然後使用 **Remove-AzureRmAppServicePlan** Cmdlet，就可以刪除 App Service 方案。

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>管理 App Service Web Apps
### <a name="create-a-web-app"></a>建立 Web 應用程式
若要建立 Web 應用程式，請使用 **New-AzureRmWebApp** Cmdlet。

以下是不同參數的說明︰

* **Name**：Web 應用程式的名稱。
* **AppServicePlan**︰用來裝載 Web 應用程式的服務方案名稱。
* **ResourceGroupName**：裝載 App Service 方案的資源群組。
* **Location**：Web 應用程式位置。

使用此 Cmdlet 的範例︰

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>在 App Service 環境中建立 Web 應用程式
在 App Service 環境 (ASE) 中建立 Web 應用程式。 使用相同的 **New-AzureRmWebApp** 命令搭配額外的參數，來指定 ASE 名稱和 ASE 所屬的資源群組名稱。

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

若要深入了解 App Service 環境，請查看 [App Service 環境簡介](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>刪除現有的 Web 應用程式
若要刪除現有的 Web 應用程式，您可以使用 **Remove-AzureRmWebApp** Cmdlet，還必須指定 Web 應用程式名稱和資源群組名稱。

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>列出現有的 Web Apps
若要列出現有的 Web 應用程式，請使用 **Get-AzureRmWebApp** Cmdlet。

若要列出您的訂用帳戶之下的所有 Web 應用程式，請使用︰

    Get-AzureRmWebApp

若要列出特定資源群組之下的所有 Web 應用程式，請使用︰

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

若要取得特定的 Web 應用程式，請使用︰

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>設定現有的 Web 應用程式
若要變更現有 Web 應用程式的設定和組態，請使用 **Set-AzureRmWebApp** Cmdlet。 如需完整的參數清單，請查看 [Cmdlet 參考連結](https://msdn.microsoft.com/library/mt652487.aspx)

範例 (1)：使用此 Cmdlet 來變更連接字串

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

範例 (2)：新增或變更應用程式設定

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


範例 (3)：將 Web 應用程式設定為在 64 位元模式下執行

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>變更現有 Web 應用程式的狀態
#### <a name="restart-a-web-app"></a>重新啟動 Web 應用程式
若要重新啟動 Web 應用程式，您必須指定 Web 應用程式的名稱和資源群組。

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>停止 Web 應用程式
若要停止 Web 應用程式，您必須指定 Web 應用程式的名稱和資源群組。

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>啟動 Web 應用程式
若要啟動 Web 應用程式，您必須指定 Web 應用程式的名稱和資源群組。

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>管理 Web 應用程式發行設定檔
每個 Web 應用程式都有發佈設定檔可用來發佈您的應用程式，並可在發行設定檔上執行許多作業。

#### <a name="get-publishing-profile"></a>取得發行設定檔
若要取得 Web 應用程式的發行設定檔，請使用︰

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

這個命令會將發佈設定檔回應至命令列，以及將發行設定檔輸出至文字檔案。

#### <a name="reset-publishing-profile"></a>重設發行設定檔
若要同時對 Web 應用程式的 FTP 和 Web 部署重設發行密碼，使用︰

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>管理 Web 應用程式憑證
若要深入了解如何管理 Web 應用程式憑證，請參閱 [使用 PowerShell 的 SSL 憑證繫結](app-service-web-app-powershell-ssl-binding.md)

### <a name="next-steps"></a>後續步驟
* 若要深入了解 Azure Resource Manager PowerShell 支援，請參閱 [搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)
* 若要深入了解 App Service 環境，請參閱 [App Service 環境簡介](app-service-app-service-environment-intro.md)
* 若要深入了解如何使用 PowerShell 來管理 App Service SSL 憑證，請參閱 [使用 PowerShell 的 SSL 憑證繫結](app-service-web-app-powershell-ssl-binding.md)
* 若要了解適用於 Azure Web Apps 的 Azure Resource Manager 架構 PowerShell Cmdlet，請參閱 [Web Apps Azure Resource Manager PowerShell Cmdlet 的 Azure Cmdlet 參考](https://msdn.microsoft.com/library/mt619237.aspx)
* * 若要了解如何使用 CLI 管理 App Service，請參閱[使用適用於 Azure Web 應用程式的 Azure Resource Manager 架構 XPlat CLI。](app-service-web-app-azure-resource-manager-xplat-cli.md)


