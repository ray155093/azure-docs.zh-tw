---
title: "使用標記來組織您的 Azure 資源 | Microsoft Docs"
description: "示範如何套用標記以針對計費及管理來組織資源。"
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
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 87973df7bb1b81c2d1cbdeb31e2fabb584f7e625
ms.openlocfilehash: 61e47f479b7cb05e1aca06e12ff5997769b2097d


---
# <a name="using-tags-to-organize-your-azure-resources"></a>使用標記來組織您的 Azure 資源
資源管理員可讓您藉由套用標記以邏輯方式組織資源。 標記包含可透過您所定義屬性來識別資源的機碼/值組。 若要將資源標示為屬於相同類別，請將相同標記套用到那些資源。

當您檢視具有特定標記的資源時，您會從所有資源群組看到資源。 您不一定只能在相同資源群組中組織資源，您可以透過獨立於部署關聯性的方式組織資源。 當您需要組織資源以進行計費或管理時，標記可能特別有用。

您新增至資源或資源群組的每個標記會自動新增至訂用帳戶的全分類法。 您也可以將標記名稱預先填入訂用帳戶的分類法，而且您想要作為資源使用的值會在未來加以標記。

每個資源或資源群組最多可以有 15 個標記。 標記名稱上限為 512 個字元，且標記值上限為 256 字元。

> [!NOTE]
> 您只能將標籤套用到支援資源管理員作業的資源。 如果您透過傳統部署模型 (例如透過傳統入口網站) 建立虛擬機器、虛擬網路或儲存體，就無法將標記套用至該資源。 若要支援標記，請透過資源管理員重新部署這些資源。 所有其他資源皆支援標記。
> 
> 

## <a name="templates"></a>範本
若要在部署期間標記資源，請將 **tags** 元素新增到您正在部署的資源，並提供標記名稱和值。 標記名稱和值不需要預先存在您的訂用帳戶中。 您可以為每個資源提供最多 15 個標記。

下列範例說明含有標記的儲存體帳戶。

```json
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
```

資源管理員目前不支援處理標記名稱和值的物件。 請改為傳遞標記值的物件，但您仍然必須指定標記名稱，如以下範例所示：

```json
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
```

## <a name="portal"></a>入口網站
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Azure CLI
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST API
入口網站和 PowerShell 在幕後都使用 [資源管理員 REST API](https://docs.microsoft.com/rest/api/resources/) 。 如果您需要將標記整合到另一個環境中，您可以在資源識別碼上利用 GET 來取得標記，並使用 PATCH 呼叫來更新一組標記。

## <a name="tags-and-billing"></a>標記與計費
對於支援的服務，您可以使用標記來分組您的計費資料。 例如，隨 Azure Resource Manager 部署的虛擬機器可讓您定義並套用標籤，以組織虛擬機器的計費使用情況。 如果您執行不同組織的多個 VM，您可以使用標記根據成本中心來分組使用情況。  
您也可以使用標記來根據執行階段環境分類成本。例如，在生產環境中執行之 VM 的計費使用情況。

您可以透過 [Azure 資源使用狀況和 RateCard API](../billing-usage-rate-card-overview.md) 或使用狀況逗號分隔值 (CSV) 檔案，擷取關於標記的資訊。 您可以從 [Azure 帳戶入口網站](https://account.windowsazure.com/)或 [EA 入口網站](https://ea.azure.com)下載使用狀況檔案。 如需以程式設計方式存取帳單資訊的詳細資訊，請參閱 [深入了解 Microsoft Azure 資源耗用量](../billing-usage-rate-card-overview.md)。 若為 REST API 作業，請參閱 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。

當您下載服務 (支援附計費的標記) 的使用狀況 CSV 時，標記會出現在 [標記]  資料行。 如需詳細資訊，請參閱[了解 Microsoft Azure 帳單](../billing/billing-understand-your-bill.md)。

![查看計費中的標記](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>後續步驟
* 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。 您所定義的原則可能需要所有資源都具有特定標記的值。 如需詳細資訊，請參閱 [使用原則來管理資源和控制存取](resource-manager-policy.md)。
* 如需部署資源時使用 Azure PowerShell 的簡介，請參閱 [搭配使用 Azure PowerShell 與 Azure 資源管理員](powershell-azure-resource-manager.md)。
* 如需部署資源時使用 Azure CLI 的簡介，請參閱 [使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 資源管理](xplat-cli-azure-resource-manager.md)。
* 如需使用入口網站的簡介，請參閱 [使用 Azure 入口網站來管理您的 Azure 資源](resource-group-portal.md)  
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。




<!--HONumber=Jan17_HO1-->


