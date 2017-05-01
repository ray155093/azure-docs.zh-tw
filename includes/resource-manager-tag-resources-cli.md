若要將標籤新增至資源群組，使用 **azure 群組集**。 如果資源群組並沒有任何現有的標籤，則傳遞標籤。

```azurecli
azure group set -n tag-demo-group -t Dept=Finance
```

標記會以整體方式更新。 若要將一個標籤新增至已經有現有標籤的資源，請傳遞所有標籤。 

```azurecli
azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
```

資源群組中的資源不會繼承標籤。 若要將標籤新增至資源，使用 **azure 資源集**。 傳遞您要新增標籤的資源類型的 API 版本號碼。 如果您需要擷取 API 版本，使用下列命令，並提供您要設定之類型的資源提供者︰

```azurecli
azure provider show -n Microsoft.Storage --json
```

在結果中，尋找所需的資源類型。

```azurecli
"resourceTypes": [
{
  "resourceType": "storageAccounts",
  ...
  "apiVersions": [
    "2016-01-01",
    "2015-06-15",
    "2015-05-01-preview"
  ]
}
...
```

現在，提供該 API 版本、資源群組名稱、資源名稱、資源類型、標籤值做為參數值。

```azurecli
azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01
```

標記是直接存在於資源和資源群組中。 若要查看現有標記，請使用 **azure group show**取得資源群組和其資源即可。

```azurecli
azure group show -n tag-demo-group --json
```

這會傳回資源群組的相關中繼資料，包括它所套用的任何標記。

```azurecli
{
  "id": "/subscriptions/4705409c-9372-42f0-914c-64a504530837/resourceGroups/tag-demo-group",
  "name": "tag-demo-group",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "location": "southcentralus",
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Project": "Upgrade"
  },
  ...
}
```

您可以使用 **azure resource show**檢視特定資源的標記。

```azurecli
azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json
```

若要擷取所有具有標籤值的資源，請使用：

```azurecli
azure resource list -t Dept=Finance --json
```

若要擷取所有具有標籤值的資源群組，請使用：

```azurecli
azure group list -t Dept=Finance
```

您可以使用下列命令檢視訂用帳戶中的現有標籤︰

```azurecli
azure tag list
```
