### 最新版 PowerShell 中的標記 Cmdlet 變更

2016 年 8 月發行的 [Azure PowerShell 2.0][powershell] 在使用標記的方式上有重大變更。在繼續之前，請先檢查您的 AzureRm.Resources 模組版本。

    Get-Module -ListAvailable -Name AzureRm.Resources | Select Version

如果您上次更新 Azure PowerShell 是在 2016 年 8 月之前，您的結果應該會顯示 3.0 以下的版本。

    Version
    -------
    2.0.2

如果您在 2016 年 8 月之後已更新過 Azure PowerShell，您的結果應該會顯示 3.0 版。

    Version
    -------
    3.0.1
    
如果您的模組版本是 3.0.1 或更新版本，則您有可搭配標記使用的最新 Cmdlet。當您使用 PowerShell 資源庫、PowerShellGet 或 Web Platform Installer 來安裝或升級 Azure PowerShell 時，會自動安裝此版本的 Azure 資源模組。如果您的版本比 3.0.1 舊，您可以繼續使用該版本，但您可以考慮更新至最新版本。最新版本包括一些變更，可讓您更輕鬆地使用標記。本主題中會展示這兩種方法。

### 針對最新版本的變更更新您的指令碼 

在最新版本中，**Tags** 參數名稱已變更為 **Tag**，而類型則從 **Hashtable** 變更為 **Hashtable**。您不再需要為每個項目提供 **Name** 和 **Value**。您將改為以 **Key = "Value"** 的格式提供索引鍵-值配對。

若要變更現有的指令碼，請將 **Tags** 參數變更為 **Tag**，並根據下列範例變更標記格式。

    # Old
    New-AzureRmResourceGroup -Tags @{ Name = "testtag"; Value = "testval" } -Name $resourceGroupName -Location $location

    # New
    New-AzureRmResourceGroup -Tag @{ testtag = "testval" } -Name $resourceGroupName -Location $location 

不過，您應該注意資源群組和資源仍會在其中繼資料中傳回 **Tags** 屬性。這個屬性並沒有受到變更。

### 3\.0.1 版或更新版本

標記是直接存在於資源和資源群組中。若要查看現有標記，請使用 **Get-AzureRmResource** 檢視資源，或使用 **Get-AzureRmResourceGroup** 檢視資源群組。

我們從資源群組開始。

    Get-AzureRmResourceGroup -Name testrg1

此 Cmdlet 會傳回資源群組上的幾個位元的中繼資料，包括已套用哪些標記 (若有的話)。

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

若要擷取含有標記的資源中繼資料，請使用下列範例。

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

您會在結果中看到標記名稱。

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : testrg1
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {Dept, Environment}

使用 **Tags** 屬性來取得標記名稱和值。

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1).Tags

它會傳回下列結果︰

    Name                   Value
    ----                   -----
    Dept                   Finance
    Environment            Production

您通常會想要擷取具有特定標記和值的所有資源或資源群組，而不是檢視特定資源群組或資源的標記。若要取得含有特定標記的資源群組，請使用 **Find-AzureRmResourceGroup** Cmdlet 與 **-Tag** 參數搭配。

若要擷取具有標記值的資源群組，請使用下列格式。

    (Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 

若要取得具有特定標記和值的所有資源，請使用 **Find-AzureRmResource** Cmdlet。

    (Find-AzureRmResource -TagName Dept -TagValue Finance).Name
    
若要將標記加入至沒有現有標記的資源群組，請使用 **Set-AzureRmResourceGroup** 命令，並指定標記物件。

    Set-AzureRmResourceGroup -Name test-group -Tag @{ Dept="IT"; Environment="Test" }

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

    Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

標記會以整體方式更新。若要將一個標記加入有其他標記的資源，請使用包含您所有欲保留標記的陣列。首先，請選取現有標記，將一個標記加入該標記集，然後重新套用所有標記。

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Status="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

若要移除一個或多個標記，只需儲存不含您要移除之標記的陣列。

對於資源也是同樣程序，但您要使用 **Get-AzureRmResource** 和 **Set-AzureRmResource** Cmdlet。

若要使用 PowerShell 取得訂用帳戶內所有標記的清單，請使用 **Get-AzureRmTag** Cmdlet。

    Get-AzureRmTag
    
它會傳回標記名稱，以及具有該標記的資源和資源群組數目的計數

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

您可能會看到開頭為 "hidden-" 和 "link:" 的標記。這些是內部標記，應該略過並避免變更。

使用 **New-AzureRmTag** Cmdlet 將新的標記加入分類法。這些標記會加入到自動完成中，即使它們尚未套用至任何資源或資源群組也一樣。若要移除標記名稱/值，請先從任何可能用到這個標記的資源中移除標記，再使用 **Remove-AzureRmTag** Cmdlet 從分類法中移除它。

### 3\.0.1 之前的版本

標記是直接存在於資源和資源群組中。若要查看現有標記，請使用 **Get-AzureRmResource** 檢視資源，或使用 **Get-AzureRmResourceGroup** 檢視資源群組。

我們從資源群組開始。

    Get-AzureRmResourceGroup -Name testrg1

此 Cmdlet 會傳回資源群組上的幾個位元的中繼資料，包括已套用哪些標記 (若有的話)。

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production
                    
若要擷取資源中繼資料，請使用下列範例。資源中繼資料不會直接顯示標記。

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

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

若要擷取具有標記值的資源群組，請使用下列格式。

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    
若要取得具有特定標記和值的所有資源，請使用 Find-AzureRmResource Cmdlet。

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }

若要將標記加入至沒有現有標記的資源群組，只需使用 Set-AzureRmResourceGroup 命令，並指定標記物件。

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

您可以藉由使用 Set-AzureRmResource 命令，將標記加入至沒有現有標記的資源。

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

標記會以整體方式更新。若要將一個標記加入有其他標記的資源，請使用包含您所有欲保留標記的陣列。首先，請選取現有標記，將一個標記加入該標記集，然後重新套用所有標記。

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

若要移除一個或多個標記，只需儲存不含您要移除之標記的陣列。

對於資源也是同樣程序，但您要使用 Get-AzureRmResource 和 Set-AzureRmResource Cmdlet。

若要使用 PowerShell 取得訂用帳戶內所有標記的清單，請使用 **Get-AzureRmTag** Cmdlet。

    Get-AzureRmTag
    
它會傳回標記名稱，以及具有該標記的資源和資源群組數目的計數

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

您可能會看到開頭為 "hidden-" 和 "link:" 的標記。這些是內部標記，應該略過並避免變更。

使用 **New-AzureRmTag** Cmdlet 將新的標記加入分類法。這些標記會加入到自動完成中，即使它們尚未套用至任何資源或資源群組也一樣。若要移除標記名稱/值，請先從任何可能用到這個標記的資源中移除標記，再使用 **Remove-AzureRmTag** Cmdlet 從分類法中移除它。


[powershell]: https://msdn.microsoft.com/library/mt619274(v=azure.200).aspx

<!----HONumber=AcomDC_0907_2016-->