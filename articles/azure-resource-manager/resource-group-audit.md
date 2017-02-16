---
title: "使用 Resource Manager 來稽核作業 | Microsoft Docs"
description: "在 Resource Manager 中使用活動記錄檔檢閱使用者動作和錯誤。 顯示 Azure 入口網站 PowerShell、Azure CLI 和 REST。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 0cb9d6acbe318ced7bc41e6a896d0b0fc77e2b64


---
# <a name="audit-operations-with-resource-manager"></a>使用資源管理員來稽核作業
透過活動記錄檔，您可以判斷︰

* 訂用帳戶的資源在進行哪些作業
* 誰起始作業 (雖然由後端服務起始的作業不會傳回使用者做為呼叫端)
* 作業進行的時間
* 作業的狀態
* 其他可能協助您研究作業的屬性值

[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

本主題著重於稽核作業。 若要了解如何使用活動記錄檔針對部署進行疑難排解，請參閱 [在 Azure 中針對資源群組部署進行疑難排解](resource-manager-troubleshoot-deployments-portal.md)。

您可以透過入口網站、PowerShell、Azure CLI、Insights REST API 或 [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/)擷取活動記錄檔中的資訊。

## <a name="portal-to-view-activity-logs"></a>在入口網站中檢視活動記錄檔
1. 若要透過入口網站檢視活動記錄檔，請選取 [更多服務] 和 [活動記錄檔]。
   
    ![檢視活動記錄檔](./media/resource-group-audit/select-audit-logs.png)
2. 在 [活動記錄檔]  刀鋒視窗中，您會看到訂用帳戶中所有資源群組的最新作業摘要。 它包含最新作業的清單。
   
    ![顯示動作](./media/resource-group-audit/audit-summary.png)
3. 若要限制顯示的作業數目，可以選取不同的條件。 例如，下圖顯示變更 [時間範圍] 和 [事件起始者] 欄位，以檢視特定使用者或應用程式在上個月所採取的動作。
   
    ![設定篩選選項](./media/resource-group-audit/set-filter.png)
4. 選取 [套用]  以檢視查詢的結果。
5. 如果您稍後需要再執行查詢，請選取 [儲存]  並給查詢一個名稱。
   
    ![儲存查詢](./media/resource-group-audit/save-query.png)
6. 若要自動篩選特定資源或資源群組，請選取資源刀鋒視窗中的 [活動記錄檔]  。 請注意，選取的資源會自動篩選活動記錄檔。
   
    ![依資源篩選](./media/resource-group-audit/filtered-by-resource.png)

## <a name="powershell-to-view-activity-logs"></a>用 PowerShell 檢視活動記錄檔
1. 若要擷取記錄檔項目，請執行 **Get-AzureRmLog** 命令。 您可提供額外的參數來篩選項目清單。 如果未指定開始和結束時間，則會傳回最後一個小時的項目。 例如，若要在過去一小時執行期間擷取資源群組的作業：
   
        Get-AzureRmLog -ResourceGroup ExampleGroup
   
    下列範例示範如何使用稽核記錄檔來研究指定期間所採取的作業。 以日期格式指定開始和結束日期。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
   
    或者，您可以使用日期函數來指定日期範圍，例如過去 14 天。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
2. 視您指定的開始時間而定，先前的命令可以傳回該資源群組的一長串作業。 您可以提供搜尋準則，以篩選您所尋找的結果。 例如，假如您想研究 Web 應用程式停止執行的方式，您可以執行下列命令，  
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
   
    並藉此了解停止動作是由 someone@contoso.com 所執行。 
   
        Authorization     :
        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Action    : Microsoft.Web/sites/stop/action
        Role      : Subscription Admin
        Condition :
        Caller            : someone@contoso.com
        CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
        EventSource       : Administrative
        EventTimestamp    : 8/28/2015 4:08:18 PM
        OperationName     : Microsoft.Web/sites/stop/action
        ResourceGroupName : ExampleGroup
        ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Status            : Succeeded
        SubscriptionId    : xxxxx
        SubStatus         : OK
3. 您可以查閱由特定使用者採取的動作，即使是針對已不存在的資源群組。
   
        Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## <a name="azure-cli-to-view-activity-logs"></a>用 Azure CLI 檢視活動記錄檔
1. 若要擷取記錄檔項目，您可執行 **azure group log show** 命令。
   
        azure group log show ExampleGroup
2. 您可以使用 JSON 公用程式 (例如 [jq](http://stedolan.github.io/jq/download/)) 來篩選結果。 下列範例示範如何尋找更新 Web 組態檔的作業。
   
        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == \"Update web sites config\")"
3. 您可以查詢特定使用者的動作。
   
        azure group log show ExampleGroup --json | jq ".[] | select(.caller==\"someone@contoso.com\")"

## <a name="rest-api-to-view-audit-logs"></a>檢視稽核記錄檔的 REST API
可用來處理活動記錄檔的 REST 作業屬於 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)的一部分。 若要擷取活動記錄檔事件，請參閱 [列出訂用帳戶中的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)。

## <a name="next-steps"></a>後續步驟
* Azure 活動記錄檔可以搭配 Power BI 用來更深入了解訂用帳戶中的動作。 請參閱 [在 Power BI 和其他工具中檢視和分析 Azure 活動記錄檔](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)。
* 如要了解如何設定安全性原則，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。
* 若要了解針對部署進行疑難排解的命令，請參閱 [在 Azure 中針對資源群組部署進行疑難排解](resource-manager-troubleshoot-deployments-portal.md)。
* 若要了解如何防止刪除所有使用者的資源，請參閱 [使用 Azure Resource Manager 鎖定資源](resource-group-lock-resources.md)。




<!--HONumber=Nov16_HO3-->


