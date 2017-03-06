AzureRm.Resources 模組的 3.0 版包含如何處理標記中的重大變更。 繼續之前，請檢查您的版本︰

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

如果結果顯示的是版本 3.0 或更新版本，本主題中的範例可適用於您的環境。 如果版本不是 3.0 或更新版本，請在進行本主題之前，先使用 PowerShell 資源庫或 Web Platform Installer 來[更新版本](/powershell/azureps-cmdlets-docs/)。

```powershell
Version
-------
3.5.0
```

每當您將標籤套用到資源或資源群組時，即會覆寫該資源或資源群組上現有的標籤。 因此，您必須視該資源或資源群組是否有想要保留的現有標籤，來使用不同的方法。 若要將標籤加入至以下項目：

* 沒有現有標籤的資源群組。

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* 擁有現有標籤的資源群組。

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* 沒有現有標籤的資源。

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* 擁有現有標籤的資源。

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

若要將所有標籤從資源群組套用至其資源，而**不在資源上保留現有的標籤**，請使用下列指令碼︰

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

若要將所有標籤從資源群組套用至其資源，並**在資源上保留所有非重複的現有標籤**，請使用下列指令碼︰

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

若要移除所有標籤，請傳遞空的雜湊表。

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

若要取得含有特定標籤的資源群組，請使用 `Find-AzureRmResourceGroup` Cmdlet。

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

若要取得具有特定標籤和值的所有資源，請使用 `Find-AzureRmResource` Cmdlet。

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

