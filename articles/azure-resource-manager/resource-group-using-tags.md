---
title: "針對邏輯組織標記 Azure 資源 | Microsoft Docs"
description: "示範如何套用標籤以針對計費及管理來組織 Azure 資源。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: 9345971b8dff683c7d079c29168437f601633eb1
ms.contentlocale: zh-tw
ms.lasthandoff: 07/18/2017

---
# <a name="use-tags-to-organize-your-azure-resources"></a>使用標記來組織 Azure 資源
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> 您只能將標籤套用到支援資源管理員作業的資源。 如果您透過傳統部署模型 (例如透過傳統入口網站) 建立虛擬機器、虛擬網路或儲存體，就無法將標記套用至該資源。 若要支援標記，請透過資源管理員重新部署這些資源。 所有其他資源皆支援標記。
> 
> 

## <a name="ensure-tag-consistency-with-policies"></a>確保標籤與原則的一致性

資源原則可讓您建立組織適用的標準規則。 您可以建立原則，以確保會為資源標記適當的值。 如需詳細資訊，請參閱[套用標籤的資源原則](resource-manager-policy-tags.md)。

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Azure CLI

若要查看**資源群組**的現有標籤，請使用：

```azurecli
az group show -n examplegroup --query tags
```

它會傳回下列格式︰

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

若要查看**具有指定資源識別碼之資源**的現有標籤，請使用：

```azurecli
az resource show --id {resource-id} --query tags
```

或者，若要查看**具有指定名稱、類型及資源群組之資源**的現有標籤，請使用：

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

若要取得具有特定標籤的資源群組，請使用 `az group list`。

```azurecli
az group list --tag Dept=IT
```

若要取得具有特定標籤和值的所有資源，請使用 `az resource list`。

```azurecli
az resource list --tag Dept=Finance
```

每當您將標籤套用到資源或資源群組時，即會覆寫該資源或資源群組上現有的標籤。 因此，您必須視該資源或資源群組是否具備現有標籤來使用不同的方法。 

若要將標籤新增至**不具現有標籤的資源群組**，請使用：

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

若要將標籤新增至**不具現有標籤的資源**，請使用：

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
``` 

若要將標籤新增至已經具有標籤的資源，請擷取現有的標籤、將該值重新格式化，然後使用新的標籤重新套用這些標籤： 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要將所有標籤從資源群組套用至其資源，而**不在資源上保留現有的標籤**，請使用下列指令碼︰

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups 
do 
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv) 
  for resid in $r 
  do 
    az resource tag --tags $t --id $resid
  done 
done
```

若要將所有標籤從資源群組套用至其資源，並**在資源上保留現有的標籤**，請使用下列指令碼：

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups 
do 
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv) 
  for resid in $r 
  do 
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done 
done
```


## <a name="templates"></a>範本

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>入口網站
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]


## <a name="rest-api"></a>REST API
入口網站和 PowerShell 在幕後都使用 [資源管理員 REST API](https://docs.microsoft.com/rest/api/resources/) 。 如果您需要將標記整合到另一個環境中，您可以在資源識別碼上利用 GET 來取得標記，並使用 PATCH 呼叫來更新一組標記。

## <a name="tags-and-billing"></a>標記與計費
標籤可讓您將計費資料分組。 例如，如果您執行不同組織的多個 VM，您可以使用標籤根據成本中心將使用情況分組。 您也可以使用標記來根據執行階段環境分類成本。例如，在生產環境中執行之 VM 的計費使用情況。


您可以透過 [Azure 資源使用狀況和 RateCard API](../billing/billing-usage-rate-card-overview.md) 或使用狀況逗號分隔值 (CSV) 檔案，擷取關於標記的資訊。 您可以從 [Azure 帳戶入口網站](https://account.windowsazure.com/)或 [EA 入口網站](https://ea.azure.com)下載使用狀況檔案。 如需以程式設計方式存取帳單資訊的詳細資訊，請參閱 [深入了解 Microsoft Azure 資源耗用量](../billing/billing-usage-rate-card-overview.md)。 若為 REST API 作業，請參閱 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。


當您下載服務 (支援附計費的標記) 的使用狀況 CSV 時，標記會出現在 [標記]  資料行。 如需詳細資訊，請參閱[了解 Microsoft Azure 帳單](../billing/billing-understand-your-bill.md)。

![查看計費中的標記](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>後續步驟
* 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。 您所定義的原則可能需要所有資源都具有特定標記的值。 如需詳細資訊，請參閱 [使用原則來管理資源和控制存取](resource-manager-policy.md)。
* 如需部署資源時使用 Azure PowerShell 的簡介，請參閱 [搭配使用 Azure PowerShell 與 Azure 資源管理員](powershell-azure-resource-manager.md)。
* 如需部署資源時使用 Azure CLI 的簡介，請參閱 [使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 資源管理](xplat-cli-azure-resource-manager.md)。
* 如需使用入口網站的簡介，請參閱 [使用 Azure 入口網站來管理您的 Azure 資源](resource-group-portal.md)  
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。


