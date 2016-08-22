<properties 
	pageTitle="將資源移到新的資源群組 | Microsoft Azure" 
	description="使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2016" 
	ms.author="tomfitz"/>

# 將資源移動到新的資源群組或訂用帳戶

此主題說明如何將資源從某個資源群組移動到另一個資源群組。您也可以將資源移動到新的訂用帳戶 (不過此訂用帳戶必須存在於相同[租用戶](./active-directory/active-directory-howto-tenant.md)中)。在下列情況中，您可能需要移動資源：

1. 因計費之目的，資源必須位於不同的訂用帳戶。
2. 資源不會再與先前的相同群組資源共用相同的生命週期。您想要將它移動到新的資源群組，以便您可以將該資源與其他資源分開管理。

當移動資源時，會在作業期間鎖定來源群組和目標群組。群組上的寫入和刪除作業將會封鎖，直到移動完成。

您無法變更資源的位置。移動資源只會將它移動到新的資源群組。新的資源群組可能會有不同的位置，但那樣不會變更資源的位置。

> [AZURE.NOTE] 本文說明如何在現有的 Azure 帳戶提供項目內移動資源。如果您真的想要變更 Azure 帳戶提供項目 (例如，從隨用隨付升級為預付)，同時繼續使用現有的資源，請參閱[切換至不同的 Azure 訂用帳戶優惠](billing-how-to-switch-azure-offer.md)。

## 移動資源前的檢查清單

在移動資源之前，要執行的重要步驟如下︰藉由驗證這些條件，您可以避免錯誤。

1. 服務必須支援移動資源的功能。請參閱下方的清單，以取得哪些[服務支援移動資源](#services-that-support-move)的相關資訊。
2. 必須針對要移動之資源的資源提供者註冊其目的地訂用帳戶。否則，您會收到錯誤，指出**未針對資源類型註冊訂用帳戶**。將資源移至新的訂用帳戶時，可能會因為該訂用帳戶不曾以指定的資源類型使用過而遇到問題。若要了解如何檢查註冊狀態及註冊資源提供者，請參閱[資源提供者和類型](../resource-manager-supported-services.md#resource-providers-and-types)。
3. 如果您使用 Azure PowerShell 或 Azure CLI，請使用最新版本。若要更新您的版本，執行 Microsoft Web Platform Installer 並檢查是否有新的版本可用如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，以及[安裝 Azure CLI](xplat-cli-install.md)。
4. 如果您正在移動 App Service 應用程式，則已檢閱 [App Service 限制](#app-service-limitations)。
5. 如果您正在移動透過傳統模型所部署的資源，則已檢閱[傳統部署限制](#classic-deployment-limitations)。

## 支援移動的服務

目前支援移動到新資源群組與訂用帳戶的服務有：

- API 管理
- App Service 應用程式 (Web 應用程式) - 請參閱 [App Service 限制](#app-service-limitations)
- 自動化
- 批次
- CDN
- 雲端服務 - 請參閱[傳統部署限制](#classic-deployment-limitations)
- Data Factory
- DNS
- DocumentDB
- HDInsight 叢集
- 金鑰保存庫
- 媒體服務
- Mobile Engagement
- 通知中樞
- Operational Insights
- Redis 快取
- 排程器
- 搜尋
- 儲存體
- 儲存體 (傳統) - 請參閱[傳統部署限制](#classic-deployment-limitations)
- SQL Database 伺服器 - 資料庫和伺服器必須位於相同的資源群組。當您移動 SQL 伺服器時，其所有資料庫也會跟著移動。
- 虛擬機器
- 虛擬機器 (傳統) - 請參閱[傳統部署限制](#classic-deployment-limitations)
- 虛擬網路

## 不支援移動的服務

目前不支援移動資源的服務有：

- 應用程式閘道
- Application Insights
- ExpressRoute
- 虛擬機器擴展集
- 虛擬網路 (傳統) - 請參閱[傳統部署限制](#classic-deployment-limitations)
- VPN 閘道

## App Service 限制

使用 App Service 應用程式時，您無法只移動 App Service 方案。若要移動 App Service 應用程式，您的選項如下：

- 將該資源群組中的 App Service 方案和所有其他 App Service 資源，都移到還沒有 App Service 資源的新資源群組。這表示甚至會移動未與 App Service 方案相關聯的 App Service 資源。
- 將應用程式移到不同的資源群組，但在原始資源群組中保留所有 App Service 方案。

如果原始資源群組也包含 Application Insights 資源，則無法移動該資源，因為 Application Insights 目前不支援移動作業。如果您在移動 App Service 應用程式時包含 Application Insights 資源，則整個移動作業會失敗。不過，Application Insights 和 App Service 方案不需要位於與應用程式相同的資源群組，應用程式就能正確運作。

例如，如果您的資源群組包含︰

- 與 **plan-a** 和 **app-insights-a** 關聯的 **web-a**
- 與 **plan-b** 和 **app-insights-b** 關聯的 **web-b**

您的選項如下：

- 移動 **web-a**、**plan-a**、**web-b** 及 **plan-b**
- 移動 **web-a** 和 **web-b**
- 移動 **web-a**
- 移動 **web-b**

所有其他組合包含移動無法移動的資源類型 (Application Insights) 或留下移動 App Service 方案時無法留下的資源類型 (任何類型的 App Service 資源)。

如果 Web 應用程式與其 App Service 方案位於不同的資源群組，但您想要將兩者移到新的資源群組，則必須使用兩個步驟來執行移動。例如：

- **web-a** 位於 **web-group** 中
- **plan-a** 位於 **plan-group** 中
- 您想要讓 **web-a** 和 **plan-a** 位於 **combined-group** 中

若要完成這項移動，請依下列序列執行兩個不同的移動作業︰

1. 將 **web-a** 移到 **plan-group**
2. 將 **web-a** 和 **plan-a** 移到 **combined-group**。

## 傳統部署限制

移動透過傳統模型所部署之資源的選項，會根據移動訂用帳戶內的資源還是將資源移到新的訂用帳戶而有所不同。

將資源從一個資源群組移到「相同訂用帳戶內」的另一個資源群組時，適用下列限制︰

- 無法移動虛擬網路 (傳統)。
- 虛擬機器 (傳統) 必須與雲端服務一起移動。
- 只有在移動包含其所有虛擬機器時，才能移動雲端服務。
- 一次只能移動一個雲端服務。
- 一次只能移動一個儲存體帳戶 (傳統)。
- 透過相同的作業，儲存體帳戶 (傳統) 不能與虛擬機器或雲端服務一起移動。

將資源移到「新訂用帳戶」時，適用下列限制︰

- 必須透過相同的作業移動訂用帳戶中的所有傳統資源。
- 只能透過入口網站或透過傳統移動的個別 REST API，來要求這項移動。將傳統資源移到新的訂用帳戶時，標準 Resource Manager 移動命令無法運作。下列各節會顯示入口網站或 REST API 的使用步驟。

## 使用入口網站移動資源

若要移動資源，請選取該資源，然後選取 [移動] 按鈕。

![移動資源](./media/resource-group-move-resources/move-resources.png)

> [AZURE.NOTE] 目前並非所有資源都支援透過入口網站移動。如果您想要移動的資源沒有 [移動] 按鈕，請使用 PowerShell、CLI 或 REST API 來移動資源。

移動資源時，您可以指定目的地訂用帳戶和資源群組。如果其他資源必須連同此資源移動，便會列出這些資源。

![選取目的地](./media/resource-group-move-resources/select-destination.png)

在 [通知] 中，您會看到移動作業正在執行。

![顯示移動狀態](./media/resource-group-move-resources/show-status.png)

完成後，您會收到結果的通知。

![顯示移動結果](./media/resource-group-move-resources/show-result.png)

如需將資源移到新資源群組 (但不是訂用帳戶) 的另一個選項，請選取您想要移動的資源。

![選取要移動的資源](./media/resource-group-move-resources/select-resource.png)

選取其 [屬性]。

![選取屬性](./media/resource-group-move-resources/select-properties.png)

如果適用於此資源類型，請選取 [變更資源群組]。

![變更資源群組](./media/resource-group-move-resources/change-resource-group.png)

您可以選取要移動的資源，以及要移入它們的資源群組。

![移動資源](./media/resource-group-move-resources/select-group.png)

將透過傳統模型所部署的資源移到新的資源群組時，您可以使用資源群組名稱旁邊的 [編輯] 圖示。

![移動傳統資源](./media/resource-group-move-resources/edit-rg-icon.png)

選取要移動的資源，同時留意[傳統部署限制](#classic-deployment-limitations)。選取 [確定] 來開始移動。

 ![選取傳統資源](./media/resource-group-move-resources/select-classic-resources.png)
 
 將透過傳統模型所部署的資源移到新的訂用帳戶時，請使用訂用帳戶旁邊的 [編輯] 圖示。
 
 ![移到新的訂用帳戶](./media/resource-group-move-resources/edit-subscription-icon.png)
 
 針對移動，會自動選取所有傳統資源。

## 使用 PowerShell 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 **Move-AzureRmResource** 命令。

第一個範例顯示如何將某個資源移動到新的資源群組。

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

第二個範例顯示如何將多個資源移動到新的資源群組。

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

若要移動到新的訂用帳戶，請為 **DestinationSubscriptionId** 參數設定某個值。

系統會要求您確認您想要移動指定的資源。

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## 使用 Azure CLI 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 **azure resource move** 命令。下列範例說明如何將 Redis 快取移動到新的資源群組。請在 **-i** 參數中，為要移動的資源識別碼提供以逗號分隔的清單。

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
	
系統會要求您確認您想要移動指定的資源。
	
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## 使用 REST API 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請執行：

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

在要求主體中，您可以指定目標資源群組以及要移動的資源。如需有關 REST 移動作業的詳細資訊，請參閱[移動資源](https://msdn.microsoft.com/library/azure/mt218710.aspx)。

不過，若要將**傳統資源移到新的訂用帳戶**，您必須使用不同的 REST 作業。若要檢查訂用帳戶是否可以做為傳統資源之跨訂用帳戶移動中的來源或目標訂用帳戶，請使用下列作業︰

    POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
對於來源訂用帳戶，使用要求本文︰

    {
        "role": "source"
    }

對於目標訂用帳戶，使用要求本文︰

    {
        "role": "target"
    }

其中一個驗證作業的回應是︰

    {
        "status": "{status}",
        "reasons": [
            "reason1",
            "reason2"
        ]
    }

若要將所有傳統資源從某個訂用帳戶移到另一個訂用帳戶，請使用下列作業︰

    POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

使用要求本文：

    {
        "target": "/subscriptions/{target-subscription-id}"
    }



## 後續步驟
- 若要了解用於管理訂用帳戶的 PowerShell Cmdlet，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](powershell-azure-resource-manager.md)。
- 若要了解用於管理訂用帳戶的 Azure CLI 命令，請參閱[搭配使用 Azure CLI 與 Azure Resource Manager](xplat-cli-azure-resource-manager.md)。
- 若要了解用於管理訂用帳戶的入口網站功能，請參閱[使用 Azure 入口網站來管理資源](./azure-portal/resource-group-portal.md)。
- 若要了解如何將邏輯組織套用到您的資源，請參閱[使用標記來組織您的資源](resource-group-using-tags.md)。

<!---HONumber=AcomDC_0810_2016---->