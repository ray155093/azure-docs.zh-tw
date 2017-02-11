---
title: "適用於 Azure Web 應用程式的 Azure Resource Manager 架構跨平台命令列工具 | Microsoft Docs"
description: "深入了解如何使用新的 Azure Resource Manager 架構跨平台命令列工具來管理 Azure Web Apps。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: f595be46983bf07783b529de885d889c18fdb61a
ms.openlocfilehash: 6f75781af24d1ad4cb6460f0dfd528684aaad57f


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-app-service"></a>使用適用於 Azure App Service 的 Azure Resource Manager 架構 XPlat CLI
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

隨著 Microsoft Azure 跨平台命令列工具 0.10.5 版的推出，已增加新的命令。 這些命令讓使用者能夠使用 Azure Resource Manager 架構 PowerShell 命令來管理 App Service。

若要了解如何管理資源群組，請參閱[使用 Azure CLI 管理 Azure 資源和資源群組](../azure-resource-manager/xplat-cli-azure-resource-manager.md)。 

> [!NOTE] 
> 此外，嘗試 [Azure CLI 2.0 (預覽)](https://github.com/Azure/azure-cli)，以 Python 撰寫，是新一代的 CLI，可供資源管理部署模型使用。
>
>

## <a name="managing-app-service-plans"></a>管理 App Service 方案
### <a name="create-an-app-service-plan"></a>建立 App Service 方案
若要建立 App Service 方案，請使用 **azure appserviceplan create** 命令。

以下是不同參數的說明︰

* **--resource-group**：包含新建立之 App Service 方案的資源群組。
* **--name**：App Service 方案的名稱。
* **--location**：App Service 方案位置。
* **--sku**：所需的定價 sku (選項包括︰F1 (免費)。 D1 (共用)。 B1 (基本小型)、B2 (基本中型) 和 B3 (基本大型)。 S1 (標準小型)、S2 (標準中型) 和 S3 (標準大型)。 P1 (進階小型)、P2 (進階中型) 和 P3 (進階大型)。
* **-instances**︰App Service 方案中的背景工作角色數目 (預設值為 1)。

使用此 Cmdlet 的範例︰

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>建立 Linux App Service 方案

使用相同 **azure appserviceplan create** 命令，搭配額外的參數 **-islinux true**。 注意 [Linux 上的 App Service 簡介](app-service-linux-intro.md)中所述的限制和區域

### <a name="list-existing-app-service-plans"></a>列出現有的 App Service 方案
若要列出現有的 App Service 方案，請使用 **azure appserviceplan list** 命令。

若要列出特定資源群組之下的所有 App Service 方案，請使用︰

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

若要取得特定的 App Service 方案，請使用 **azure appserviceplan show** 命令：

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>設定現有的 App Service 方案
若要變更現有 App Service 方案的設定，請使用 **azure appserviceplan config** 命令。 您可以變更 sku 和背景工作角色數目 

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>調整 App Service 方案
若要調整現有的 App Service 方案，請使用：

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>變更 App Service 方案的 SKU
若要變更現有 App Service 方案的 sku，請使用︰

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>刪除現有的 App Service 方案
若要刪除現有的 App Service 方案，需要先移動或刪除所有已指派的應用程式。 然後使用 **azure webapp delete** 命令，就可以刪除 App Service 方案。

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-apps"></a>管理 App Service 應用程式
### <a name="create-a-web-app"></a>建立 Web 應用程式
若要建立 Web 應用程式，請使用 **azure webapp create** 命令。

以下是不同參數的說明︰

* **--name**：Web 應用程式的名稱。
* **-plan**︰用來裝載 Web 應用程式的服務方案名稱。
* **--resource-group**：裝載 App Service 方案的資源群組。
* **--location**：Web 應用程式位置。

使用此 Cmdlet 的範例︰

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-app"></a>刪除現有的應用程式
若要刪除現有的應用程式，您可以使用 **azure webapp delete** 命令。 您需要指定應用程式的名稱和資源群組名稱。

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-apps"></a>列出現有的應用程式
若要列出現有的應用程式，請使用 **azure webapp list** 命令。

若要列出特定資源群組中的所有應用程式，請使用︰

    azure webapp list --resource-group ContosoAzureResourceGroup

若要取得特定的應用程式，請使用 **azure webapp show** 命令。

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-app"></a>設定現有的應用程式
若要變更現有應用程式的設定和組態，請使用 **azure webapp config set** 命令。

範例 (1)：變更應用程式的 php 版本 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

範例 (2)：新增或變更應用程式設定

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

若想知道還可變更其他哪些組態，請使用 **azure webapp config set -h** 命令。

### <a name="change-the-state-of-an-existing-app"></a>變更現有應用程式的狀態
#### <a name="restart-an-app"></a>重新啟動應用程式
若要重新啟動應用程式，您必須指定應用程式的名稱和資源群組。

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-an-app"></a>停止應用程式
若要停止應用程式，您必須指定應用程式的名稱和資源群組。

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-an-app"></a>啟動應用程式
若要啟動應用程式，您必須指定應用程式的名稱和資源群組。

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-publishing-profiles-of-an-app"></a>管理應用程式的發行設定檔
每個應用程式都有可用來發佈程式碼的發行設定檔。

#### <a name="get-publishing-profile"></a>取得發行設定檔
若要取得應用程式的發行設定檔，請使用︰

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

此命令會將發行設定檔使用者名稱和密碼回應到命令列。

### <a name="manage-app-hostnames"></a>管理應用程式主機名稱
若要管理應用程式的主機名稱繫結，請使用 **azure webapp config hostnames** 命令  

#### <a name="list-hostname-bindings"></a>列出主機名稱繫結
若要取得應用程式目前的主機名稱繫結，請使用︰

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>新增主機名稱繫結
若要將主機名稱繫結新增至應用程式，請使用︰

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>刪除主機名稱繫結
若要刪除主機名稱繫結，請使用︰

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

## <a name="next-steps"></a>後續步驟
* 若要了解 Azure Resource Manager 支援，請參閱[使用 Azure CLI 管理 Azure 資源和資源群組](../azure-resource-manager/xplat-cli-azure-resource-manager.md)。
* 若要了解如何使用 PowerShell 管理 App Service，請參閱[使用 Azure Resource Manager 架構 PowerShell 來管理 Azure Web Apps。](app-service-web-app-azure-resource-manager-powershell.md)
* 若要了解 Linux 上的 Azure App Service，請參閱 [Linux 上的 App Service 簡介](app-service-linux-intro.md)



<!--HONumber=Feb17_HO2-->


