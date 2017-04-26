---
title: "Azure 資源原則 | Microsoft Docs"
description: "描述如何使用 Azure Resource Manager 原則以確保在部署期間設定一致的資源內容。 原則可以套用在訂用帳戶或資源群組。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d75088bd83b0b70c889388c95331bb56fe9ba15b
ms.lasthandoff: 04/03/2017


---
# <a name="resource-policy-overview"></a>資源原則概觀
資源原則可讓您為組織中的資源建立慣例。 藉由定義慣例，您可以控制成本以及更輕鬆地管理您的資源。 例如，您可以指定僅允許特定類型的虛擬機器，或您可以要求所有資源都有特定的類型。 原則會由所有子資源繼承。 所以，如果原則套用至資源群組，它會適用於該資源群組中的所有資源。

有兩個概念可了解原則︰

* 原則定義 - 您說明何時會強制執行原則及要採取的動作
* 原則指派 - 您將原則定義套用至範圍 (訂用帳戶或資源群組)

本主題著重於原則定義。 如需原則指派的相關資訊，請參閱[使用 Azure 入口網站來指派和管理資源原則](resource-manager-policy-portal.md)或[透過指令碼來指派和管理原則](resource-manager-policy-create-assign.md)。

Azure 提供一些內建原則定義，可能會降低您需要定義的原則數目。 如果內建的原則定義適用於您的案例，則在指派給範圍時使用該定義。

建立和更新資源 (PUT 和 PATCH 作業) 時，會評估原則。

> [!NOTE]
> 目前，原則不會評估不支援標記、種類及位置的資源類型，例如 Microsoft.Resources/deployments 資源類型。 未來將加入此支援。 若要避免向下相容問題，撰寫原則時應該明確指定類型。 例如，會對所有類型套用未指定類型的標記原則。 在此情況下，如果有不支援標記的巢狀資源，且部署資源類型已新增原則評估中，範本部署可能會失敗。 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>它和 RBAC 有什麼不同？
原則和角色型存取控制 (RBAC) 之間有幾個主要差異。 RBAC 著重於不同範圍的**使用者**動作。 例如，若您被加入所需範圍內之資源群組的參與者角色中，您便能對該資源群組做出變更。 原則在部署期間著重於**資源**屬性。 例如，您能夠透過原則控制可以佈建的資源類型，或限制資源可以佈建的位置。 不同於 RBAC，原則是個預設允許和明確拒絕的系統。 

若要使用原則，您必須透過 RBAC 驗證。 具體來說，您的帳戶需要：

* 定義原則的 `Microsoft.Authorization/policydefinitions/write` 權限
* 指派原則的 `Microsoft.Authorization/policyassignments/write` 權限 

這些權限不包括在**參與者**角色中。

## <a name="policy-definition-structure"></a>原則定義結構
使用 JSON 來建立原則定義。 原則定義中包含以下的項目︰

* 參數
* 顯示名稱
* 說明
* 原則規則
  * 邏輯評估
  * 效果

下列範例示範的原則會限制資源的部署之處︰

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="parameters"></a>參數
使用參數會減少原則定義的數量，有助於簡化原則管理。 定義資源屬性的原則 (例如，限制可以在其中部署資源的位置)，並在定義中包含參數。 然後，您藉由在指派原則時傳入不同的值 (例如，指定訂用帳戶的一組位置)，針對不同的案例重複使用該原則定義。

當您建立原則定義時，宣告參數。

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

參數的類型可以是字串或陣列。 中繼資料屬性是由 Azure 入口網站等工具所使用，用來顯示人性化的資訊。 

在原則規則中，您可以使用下列語法參考參數︰ 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>顯示名稱和描述

使用 **displayName** 和 **description**找出原則定義，並提供使用時的內容。

## <a name="policy-rule"></a>原則規則

原則規則包含 **If** 和 **Then**區塊。 在 **If** 區塊中，您可以定義一個或多個指定強制執行此原則時間的條件。 您可以將邏輯運算子套用至這些條件，以精確地定義原則的案例。 在 **Then** 區塊中，定義當 **If** 條件履行時所發生的效果。

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>邏輯運算子
支援的邏輯運算子包括︰

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

**not** 語法會反轉條件的結果。 **allOf** 語法 (類似於邏輯**And** 作業) 需要所有的條件為 true。 **anyOf** 語法 (類似於邏輯**Or** 作業) 需要一或多個條件為 true。

您可以巢狀邏輯運算子。 下列範例顯示 **And** 作業內變成巢狀的 **Not** 作業。 

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>條件
條件會評估某個**欄位**是否符合特定準則。 支援的條件如下︰

* `"equals": "value"`
* `"like": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

當使用 **like**條件時，您可以在值中提供萬用字元 (*)。

### <a name="fields"></a>欄位
條件是透過欄位所形成。 欄位會顯示用來描述資源狀態的資源要求裝載屬性。  

支援下列欄位：

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* 屬性別名

您可以使用屬性別名來存取資源類型的特定屬性。 支援的別名為：

* Microsoft.CDN/profiles/sku.name
* Microsoft.Compute/virtualMachines/imageOffer
* Microsoft.Compute/virtualMachines/imagePublisher
* Microsoft.Compute/virtualMachines/sku.name
* Microsoft.Compute/virtualMachines/imageSku 
* Microsoft.Compute/virtualMachines/imageVersion
* Microsoft.SQL/servers/databases/edition
* Microsoft.SQL/servers/databases/elasticPoolName
* Microsoft.SQL/servers/databases/requestedServiceObjectiveId
* Microsoft.SQL/servers/databases/requestedServiceObjectiveName
* Microsoft.SQL/servers/elasticPools/dtu
* Microsoft.SQL/servers/elasticPools/edition
* Microsoft.SQL/servers/version
* Microsoft.Storage/storageAccounts/accessTier
* Microsoft.Storage/storageAccounts/enableBlobEncryption
* Microsoft.Storage/storageAccounts/sku.name
* Microsoft.Web/serverFarms/sku.name

### <a name="effect"></a>效果
原則支援三種效果類型 - `deny`、`audit` 和 `append`。 

* **拒絕**會在稽核記錄檔中產生事件，並且使要求失敗
* **稽核**會在稽核記錄檔中產生事件，但不會使要求失敗
* **附加**會在要求中加入一組已定義的欄位 

對於 **append**，您必須提供下列詳細資料：

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

值可以是字串或 JSON 格式物件。 

## <a name="policy-examples"></a>原則範例

下列主題包含原則範例︰

* 如需標籤原則的範例，請參閱[套用標籤的資源原則](resource-manager-policy-tags.md)。
* 如需儲存體原則的範例，請參閱[將資源原則套用至儲存體帳戶](resource-manager-policy-storage.md)。
* 如需虛擬機器原則的範例，請參閱[將資源原則套用至 Linux VM](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) 和[將資源原則套用至 Windows VM](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

### <a name="allowed-resource-locations"></a>允許的資源位置
若要指定允許的位置，請參閱[原則定義結構](#policy-definition-structure)一節中的範例。 若要指派此原則定義，請使用包含資源識別碼 `/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c` 的內建原則。

### <a name="not-allowed-resource-locations"></a>不允許的資源位置
若要指定不允許的位置，請使用下列的原則定義︰

```json
{
  "properties": {
    "parameters": {
      "notAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that are not allowed when deploying resources",
          "strongType": "location",
          "displayName": "Not allowed locations"
        }
      }
    },
    "displayName": "Not allowed locations",
    "description": "This policy enables you to block locations that your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "field": "location",
        "in": "[parameters('notAllowedLocations')]"
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="allowed-resource-types"></a>允許的資源類型
下列範例顯示的原則只允許 Microsoft.Resources、Microsoft.Compute、Microsoft.Storage、Microsoft.Network 資源類型的部署。 所有其他的則會被拒絕︰

```json
{
  "if": {
    "not": {
      "anyOf": [
        {
          "field": "type",
          "like": "Microsoft.Resources/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Compute/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Storage/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Network/*"
        }
      ]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### <a name="set-naming-convention"></a>設定命名慣例
下列範例示範如何使用 **like** 條件所支援的萬用字元。 條件指出如果名稱符合所述模式 (namePrefix\*nameSuffix)，則拒絕要求：

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>後續步驟
* 在定義原則規則後，將它指派給範圍。 若要透過入口網站來指派原則，請參閱[使用 Azure 入口網站來指派和管理資源原則](resource-manager-policy-portal.md)。 若要透過 REST API、PowerShell 或 Azure CLI 來指派原則，請參閱[透過指令碼來指派和管理原則](resource-manager-policy-create-assign.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。
* 原則結構描述會發佈於 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json)。 


