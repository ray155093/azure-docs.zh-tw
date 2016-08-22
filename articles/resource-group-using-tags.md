<properties
	pageTitle="使用標記來組織您的 Azure 資源 | Microsoft Azure"
	description="示範如何套用標記以針對計費及管理來組織資源。"
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="tomfitz"/>


# 使用標記來組織您的 Azure 資源

資源管理員可讓您藉由套用標記以邏輯方式組織資源。標記包含可透過您所定義屬性來識別資源的機碼/值組。若要將資源標示為屬於相同類別，請將相同標記套用到那些資源。

當您檢視具有特定標記的資源時，您會從所有資源群組看到資源。您不一定只能在相同資源群組中組織資源，您可以透過獨立於部署關聯性的方式組織資源。當您需要組織資源以進行計費或管理時，標記可能特別有用。

您新增至資源或資源群組的每個標記會自動新增至訂用帳戶的全分類法。您也可以將標記名稱預先填入訂用帳戶的分類法，而且您想要作為資源使用的值會在未來加以標記。

每個資源或資源群組最多可以有 15 個標記。標記名稱上限為 512 個字元，且標記值上限為 256 字元。

> [AZURE.NOTE] 您只能將標籤套用到支援資源管理員作業的資源。如果您透過傳統部署模型 (例如透過傳統入口網站) 建立虛擬機器、虛擬網路或儲存體，就無法將標記套用至該資源。若要支援標記，請透過資源管理員重新部署這些資源。所有其他資源皆支援標記。

## 範本

若要在部署期間標記資源，只需將 tags 元素加入至您正在部署的資源，並提供標記名稱和值。標記名稱和值不需要預先存在您的訂用帳戶中。您可以為每個資源提供最多 15 個標記。

下列範例說明含有標記的儲存體帳戶。

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

資源管理員目前不支援處理標記名稱和值的物件。您可以轉而傳遞標記值的物件，但您仍必須指定標記名稱，如下面範例所示。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## 入口網站

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## PowerShell

標記是直接存在於資源和資源群組中。若要查看現有標記，只要使用 **Get-AzureRmResource** 或 **Get-AzureRmResourceGroup** 取得資源或資源群組即可。我們從資源群組開始。

    Get-AzureRmResourceGroup -Name tag-demo-group

此 Cmdlet 會傳回資源群組上的幾個位元的中繼資料，包括已套用哪些標記 (若有的話)。

    ResourceGroupName : tag-demo-group
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

當取得資源的中繼資料時，這些標記不會直接顯示。

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group

您會在結果中看到標記只會顯示為雜湊表物件。

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

您可以藉由擷取 **Tags** 屬性來檢視實際標記。

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
   
這會傳回格式化的結果︰
    
    Dept: Finance
    Environment: Production

您通常會想要擷取具有特定標記和值的所有資源或資源群組，而不是檢視特定資源群組或資源的標記。若要取得含有特定標記的資源群組，請使用 **Find-AzureRmResourceGroup** Cmdlet 與 **-Tag** 參數搭配。

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    
這會傳回具有該標記值的資源群組名稱。
   
    tag-demo-group
    web-demo-group

若要取得具有特定標記和值的所有資源，請使用 **Find-AzureRmResource** Cmdlet。

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
    
這會傳回具有該標記值的資源名稱。
    
    tfsqlserver
    tfsqldatabase

若要將標記加入至沒有現有標記的資源群組，只需使用 **Set-AzureRmResourceGroup** 命令，並指定標記物件。

    Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

這會傳回資源群組稱與其新的標記值。

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
您可以藉由使用 **Set-AzureRmResource** 命令，將標記加入至沒有現有標記的資源

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

標記會以整體方式更新。若要將一個標記加入有其他標記的資源，請使用包含您所有欲保留標記的陣列。首先，請選取現有標記，將一個標記加入該標記集，然後重新套用所有標記。

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

若要移除一個或多個標記，只需儲存不含您要移除之標記的陣列。

對於資源也是同樣程序，但您要使用 **Get-AzureRmResource** 和 **Set-AzureRmResource** Cmdlet。

若要使用 PowerShell 取得訂用帳戶內所有標記的清單，請使用 **Get-AzureRmTag** Cmdlet。

    Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

您可能會看到開頭為 "hidden-" 和 "link:" 的標記。這些是內部標記，應該略過並避免變更。

使用 **New-AzureRmTag** Cmdlet 將新的標記加入分類法。這些標記會加入到自動完成中，即使它們尚未套用至任何資源或資源群組也一樣。若要移除標記名稱/值，請先從任何可能用到這個標記的資源中移除標記，再使用 **Remove-AzureRmTag** Cmdlet 從分類法中移除它。

## Azure CLI

標記是直接存在於資源和資源群組中。若要查看現有標記，只要使用 **azure group show** 取得資源群組和其資源即可。

    azure group show -n tag-demo-group
    
這會傳回資源群組的相關中繼資料，包括它所套用的任何標記。
    
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

若只要取得資源群組的標記，請使用 JSON 公用程式，例如 [jq](http://stedolan.github.io/jq/download/)。

    azure group show -n tag-demo-group --json | jq ".tags"
    
這會傳回該資源群組的標記。
    
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

您可以使用 **azure resource show** 檢視特定資源的標記。

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    
這會傳回該資源的標記。
    
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
以下範例說明如何擷取有標記名稱與值的所有資源。

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    
這會傳回具有該標記的資源名稱。
    
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

標記會以整體方式更新。若要將一個標記加入已經有現有標記的資源，請擷取您欲保留的所有現有標記。若要設定資源群組的標記值，請使用 **azure group set** 並提供資源群組的所有標記。

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    
隨即傳回資源群組的摘要和新標記。
    
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
您可以使用 **azure tag list** 列出您的訂用帳戶中的現有標記，使用 **azure tag create** 加入標記。若要從您訂用帳戶的分類中移除某個標記，請先將該標記從任何資源中移除。然後，使用 **azure tag delete** 移除標記。

## REST API

入口網站和 PowerShell 在幕後都使用[資源管理員 REST API](https://msdn.microsoft.com/library/azure/dn848368.aspx)。如果您需要將標記整合到另一個環境中，您可以在資源識別碼上利用 GET 來取得標記，並使用 PATCH 呼叫來更新一組標記。


## 標記與計費

對於支援的服務，您可以使用標記來分組您的計費資料。例如，[和 Azure 資源管理員整合的虛擬機器](./virtual-machines/virtual-machines-windows-compare-deployment-models.md)可讓您定義並套用標記，以組織虛擬機器的計費使用情況。如果您執行不同組織的多個 VM，您可以使用標記根據成本中心來分組使用情況。您也可以使用標記來根據執行階段環境分類成本。例如，在生產環境中執行之 VM 的計費使用情況。

您可以透過 [Azure 資源使用狀況和 RateCard API](billing-usage-rate-card-overview.md) 或使用狀況逗號分隔值 (CSV) 檔案，擷取關於標記的資訊。您可以從 [Azure 帳戶入口網站](https://account.windowsazure.com/)或 [EA 入口網站](https://ea.azure.com)下載使用狀況檔案。如需以程式設計方式存取帳單資訊的詳細資訊，請參閱[深入了解 Microsoft Azure 資源耗用量](billing-usage-rate-card-overview.md)。若為 REST API 作業，請參閱 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。

當您下載服務 (支援附計費的標記) 的使用狀況 CSV 時，標記會出現在 [標記] 資料行。若需更多詳細資料，請參閱[了解 Microsoft Azure 的計費](billing-understand-your-bill.md)。

![查看計費中的標記](./media/resource-group-using-tags/billing_csv.png)

## 後續步驟

- 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。您所定義的原則可能需要所有資源都具有特定標記的值。如需詳細資訊，請參閱[使用原則來管理資源和控制存取](resource-manager-policy.md)。
- 如需部署資源時使用 Azure PowerShell 的簡介，請參閱[搭配使用 Azure PowerShell 與 Azure 資源管理員](./powershell-azure-resource-manager.md)。
- 如需部署資源時使用 Azure CLI 的簡介，請參閱[使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 資源管理](./xplat-cli-azure-resource-manager.md)。
- 如需使用入口網站的簡介，請參閱[使用 Azure 入口網站來管理您的 Azure 資源](./azure-portal/resource-group-portal.md)

<!---HONumber=AcomDC_0810_2016-->