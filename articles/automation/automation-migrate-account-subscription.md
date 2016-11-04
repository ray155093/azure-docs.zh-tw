---
title: 移轉自動化帳戶和資源 | Microsoft Docs
description: 本文章說明如何將 Azure 自動化中的自動化帳戶和相關聯的資源，從某一個訂用帳戶移到另一個訂用帳戶。
services: automation
documentationcenter: ''
author: MGoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/07/2016
ms.author: magoedte

---
# 移轉自動化帳戶和資源
對於您已在 Azure 入口網站中建立而且想要在資源群組間或訂用帳戶間移轉的自動化帳戶及其相關聯的資源 (即資產、Runbook、模組 等等)，您可以透過 Azure 入口網站中可用的[移動資源](../resource-group-move-resources.md)功能輕鬆完成這項作業。不過，在繼續此動作之前，您應該先檢閱下列[檢查清單再移動資源](../resource-group-move-resources.md#Checklist-before-moving-resources)，此外，還需檢閱以下自動化專用的清單。

1. 目的地訂用帳戶/資源群組必須位於與來源相同的區域。這表示，無法跨區域移動自動化帳戶。
2. 移動資源 (如 Runbook、作業等) 時，會在作業期間鎖定來源群組和目標群組。群組上的寫入和刪除作業將會封鎖，直到移動完成。
3. 在移轉完成之後，需要更新任何參考現有訂用帳戶中資源或訂用帳戶識別碼的 Runbook 或變數。

> [!NOTE]
> 這項功能不支援移動傳統自動化資源。
> 
> 

## 使用入口網站移動自動化帳戶
1. 從您的自動化帳戶，按一下刀鋒視窗頂端的 [移動]。<br> ![移動選項](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. 在 [移動資源] 刀鋒視窗上，請注意它會顯示與您的自動化帳戶和資源群組相關的資源。選取下拉式清單中的**訂用帳戶**和**資源群組**，或選取 [建立新的資源群組] 選項並在提供的欄位中輸入新的資源群組名稱。
3. 檢閱並選取核取方塊，以確認您「了解工具和指令碼需要更新，才能在移動資源後使用新的資源識別碼」，然後按一下 [確定]。<br> ![移動資源刀鋒視窗](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>

此動作需要幾分鐘時間才能完成。在 [通知] 中，您會看到所進行的每個動作 (驗證、移轉) 的狀態，然後最後是完成的時間。

## 使用 PowerShell 移動自動化帳戶
若要將現有的自動化資源移至另一個資源群組或訂用帳戶，請使用 **Get-AzureRmResource** cmdlet 來取得特定的自動化帳戶，然後使用 **Move-AzureRmResource** cmdlet 執行移動。

第一個範例示範如何將自動化帳戶移到新的資源群組。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ``` 

執行上述程式碼範例之後，系統會提示您確認您要執行此動作。一旦按一下 [是] 並允許繼續執行指令碼，您就不會在執行移轉時收到任何通知。

若要移動到新的訂用帳戶，請為 *DestinationSubscriptionId* 參數設定某個值。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ``` 

如同上述範例，系統會提示您確認移動。

## 後續步驟
* 如需將資源移到新的資源群組或訂用帳戶的詳細資訊，請參閱[將資源移到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。
* 如需 Azure 自動化中角色型存取控制的詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
* 若要了解用於管理訂用帳戶的 PowerShell Cmdlet，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。
* 若要了解用於管理訂用帳戶的入口網站功能，請參閱[使用 Azure 入口網站來管理資源](../azure-portal/resource-group-portal.md)。

<!---HONumber=AcomDC_0713_2016-->