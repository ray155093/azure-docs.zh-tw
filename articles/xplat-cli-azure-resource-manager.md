
<properties
	pageTitle="使用 Azure CLI 管理資源 | Microsoft Azure"
	description="使用 Azure 命令列介面 (CLI) 來管理 Azure 資源和群組"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="danlep"/>

# 使用 Azure CLI 管理 Azure 資源和資源群組


> [AZURE.SELECTOR]
- [入口網站](azure-portal/resource-group-portal.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [.NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [節點](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


Azure 命令列介面 (Azure CLI) 是您可以使用 Resource Manager 來部署和管理資源的其中一個工具。本文將介紹以 Resource Manager 模式使用 Azure CLI 來管理 Azure 資源和資源群組的常見方式。如需使用 CLI 來部署資源的相關資訊，請參閱[使用 Resource Manager 與 Azure 入口網站來部署資源](resource-group-template-deploy-cli.md)。如需 Azure 資源和 Resource Manager 的背景，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。

>[AZURE.NOTE] 若要以 Azure CLI 管理 Azure 資源，您需要[安裝 Azure CLI](xplat-cli-install.md) 並使用 `azure login` 命令[登入 Azure](xplat-cli-connect.md)。請確定 CLI 是處於 Resource Manager 模式 (執行 `azure config mode arm`)。如果上述事項都已完成，您就能開始進行了。



## 取得資源群組和資源

### 資源群組

若要取得您的訂用帳戶中所有資源群組及其位置的清單，請執行此命令。

    azure group list
    

### 資源
 若要列出群組中的所有資源，例如名為 *testRG* 的資源，請使用下列命令。

	azure resource list testRG

若要檢視群組內的個別資源 (例如名為「MyUbuntuVM」的 VM)，請使用如下所示的命令。

	azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
請注意 **Microsoft.Compute/virtualMachines** 參數。此參數表示您正在要求哪類資源的資訊。
    
>[AZURE.NOTE]使用 **list** 命令以外的**azure resource** 命令時，必須以**-o** 參數指定資源的 API 版本。如果不確定要使用的 API 版本，請咨詢範本檔案並尋找資源的 apiVersion 欄位。如需 Resource Manager 中的 API 版本詳細資訊，請參閱 [Resource Manager 提供者、區域、API 版本和結構描述](resource-manager-supported-services.md)。

檢視資源的詳細資料時，建議使用 `--json` 參數，更加實用。這個參數可讓輸出更容易閱讀，因為部分值是巢狀的結構或集合。下列範例示範如何以 JSON 文件傳回 **show** 命令的結果。

	azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] 若您希望，可以使用 &gt; 字元將輸出導向檔案，藉此將 JSON 資料儲存至檔案。例如：
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### 標記

為協助您組織資源，請將[標籤](resource-group-using-tags.md)新增至資源和資源群組。若要查看已經套用哪些標籤，只要使用 **azure group show** 取得資源群組和其資源即可。

    azure group show -n tag-demo-group
    
此命令會傳回資源群組的相關中繼資料，包括套用至其上的任何標籤。
    
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
    
此命令會傳回該資源群組的標籤。
    
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

使用 **azure resource show** 來檢視特定資源的標籤。

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    
此命令會傳回下列項目。
    
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
使用類似下列的命令，以擷取具有特定標籤的所有資源。

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    
此命令會傳回具有該標籤的資源名稱。
    
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

標籤是整體更新，因此，如果您要將一個標籤新增至已標記的資源，則必須擷取您想要保留的現有標籤。若要設定資源群組的標記值，請使用 **azure group set** 並提供資源群組的所有標記。

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    
隨即傳回資源群組的摘要和新標記。
    
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
您可以使用 **azure tag list** 列出您的訂用帳戶中的現有標記，使用 **azure tag create** 加入標記。若要從您的訂用帳戶的分類移除標籤，請先從其搭配使用的任何資源移除標籤，然後使用 **azure tag delete** 移除標籤。

## 管理資源


若要將例如儲存體帳戶的資源新增至資源群組，請執行類似下列的命令︰

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{"accountType": "Standard_LRS"}"
    
除了以**-o** 參數指定資源的 API 版本，請使用**-p** 參數來傳遞任何具有必要或其他屬性的 JSON 格式字串。
    
    
若要刪除現有的資源，例如虛擬機器資源，請使用類似下列的命令。

	azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 **azure resource move** 命令。下列範例說明如何將 Redis 快取移動到新的資源群組。請在 **-i** 參數中，為要移動的資源識別碼提供以逗號分隔的清單。


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## 控制資源的存取

您可以使用 Azure CLI 來建立和管理原則，以控制 Azure 資源的存取權。如需原則定義和將原則指派給資源的背景，請參閱[使用原則來管理資源並控制存取](resource-manager-policy.md)。

例如，定義下列的原則，以拒絕所有位置不在美國西部或美國中北部的要求，並將其儲存到原則定義檔案 policy.json：

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

然後執行 **policy definition create** 命令︰

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
此命令會顯示類似下列的輸出。

    + Creating policy definition MyPolicy
    data:    PolicyName:             MyPolicy
    data:    PolicyDefinitionId:     /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    data:    PolicyType:             Custom
    data:    DisplayName:            undefined
    data:    Description:            undefined
    data:    PolicyRule:             field=location, in=[westus, northcentralus], effect=deny

 若要在您想要的範圍內指派原則，請使用從前一個命令傳回的 **PolicyDefinitionId**。在下列範例中，此範圍是訂用帳戶，但您可以將範圍設為資源群組或個別資源︰

    azure policy assignment create MyPolicyAssignment -p /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/########-####-####-####-############/

您可以使用 **policy definition show**、**policy definition set** 和 **policy definition delete** 命令來取得、變更或移除原則定義。

同樣地，您可以使用 **policy assignment show**、**policy assignment set** 和 **policy assignment delete** 命令來取得、變更或移除原則指派。


## 匯出資源群組作為範本

針對現有的資源群組，您可以檢視此資源群組的 Resource Manager 範本。匯出此範本有兩個優點︰

1. 因為所有基礎結構都已定義於範本中，所以您可以輕鬆地自動進行解決方案的未來部署。

2. 您可以查看代表您的解決方案的 JSON，藉此熟悉範本語法。

使用 Azure CLI，您可以匯出代表資源群組目前狀態的範本，或下載特定部署所用的範本。

* **匯出資源群組的範本p** - 這在您已變更資源群組，而且需要擷取其目前狀態的 JSON 表示法時很有用。不過，產生的範本只包含最少的參數數目，但不包含任何變數。範本中大部分的值為硬式編碼。在部署所產生的範本之前，您可能想要將更多的值轉換成參數，以便針對不同的環境自訂部署。

    若要將資源群組範本匯出至本機目錄中，請執行 `azure group export` 命令，如下列範例所示。(取代適用於您作業系統環境的本機目錄。)

        azure group export testRG ~/azure/templates/

* **下載特定部署的範本** - 這在您需要檢視用來部署資源的實際範本時很有用。範本會包含針對原始部署定義的所有參數和變數。不過，如果您組織中有人已變更非此範本中定義的資源群組，此範本並不會代表資源群組的目前狀態。

    若要將用於特定部署的範本下載至本機目錄，請執行 `azure group deployment template download` 命令。例如：

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] 範本匯出處於預覽狀態，並非所有的資源類型目前都支援匯出範本。嘗試匯出範本時，您可能會看到一個錯誤，表示未匯出某些資源。如有需要，下載範本之後，在範本中手動定義這些資源。



## 後續步驟

* 若要取得部署作業的詳細資料，並使用 Azure CLI 進行部署錯誤的疑難排解，請參閱[使用 Azure CLI 檢視部署作業](resource-manager-troubleshoot-deployments-cli.md)。
* 如果您想要使用 CLI 來設定應用程式或指令碼以存取資源，請參閱[使用 Azure CLI 來建立服務主體以存取資源](resource-group-authenticate-service-principal-cli.md)。

<!---HONumber=AcomDC_0824_2016-->