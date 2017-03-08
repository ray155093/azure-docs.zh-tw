---
title: "標籤的 Azure 資源原則 | Microsoft Docs"
description: "提供在資源上管理標籤之資源原則的範例"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 72d398c529fc7dd5eef450da0e134dcdab534ac5
ms.openlocfilehash: 375a8df763eb6b4b8f7349e0061ab39c076ebfc6
ms.lasthandoff: 02/15/2017


---
# <a name="apply-resource-policies-for-tags"></a>套用標籤的資源原則

本主題提供可套用以確保一致地在資源上使用標籤的通用原則規則。

使用現有的資源將標籤原則套用到資源群組或訂用帳戶不會追溯性地將原則套用到這些資源。 若要在這些資源上強制原則，將更新觸發至現有的資源，如[將更新觸發至現有的資源](#trigger-updates-to-existing-resources)中所示。

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>請確定資源群組中的所有資源都有標籤/值

常見的需求是資源群組中的所有資源都有特定的標籤和值。 通常需要這項需求來依部門追蹤成本。 必須符合下列條件：

* 必要的標籤和值會附加至沒有任何現有標籤的新增和更新的資源。
* 必要的標籤和值會附加至有其他標籤的新增和更新的資源，但不會附加至必要的標籤和值。
* 無法從任何現有的資源移除必要的標籤和值。

將下列三個原則套用至資源群組來完成這項需求︰

* [附加標籤](#append-tag) 
* [使用其他標籤附加標籤](#append-tag-with-other-tags)
* [需要標籤和值](#require-tag-and-value)

### <a name="append-tag"></a>附加標籤

下列原則規則會在沒有標籤時附加 costCenter 標籤，並且具有預先定義的值：

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### <a name="append-tag-with-other-tags"></a>使用其他標籤附加標籤

下列原則規則會在有標籤 (但未定義 costCenter 標籤) 時附加 costCenter 標籤，並且具有預先定義的值：

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### <a name="require-tag-and-value"></a>需要標籤和值

下列原則規則會拒絕更新或建立沒有將 costCenter 標籤指派至預先定義值的資源。

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="require-tags-for-a-resource-type"></a>需要資源類型的標籤
下列範例說明如何建立巢狀邏輯運算子，只為指定的資源類型 (在此案例中為儲存體帳戶) 要求應用程式標籤。

```json
{
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
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>需要標籤
下列原則會拒絕沒有包含 "costCenter" 索引鍵標籤的要求 (可套用任何值)：

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="trigger-updates-to-existing-resources"></a>將更新觸發至現有的資源

下列 PowerShell 指令碼會將更新觸發至現有的資源，以強制執行您已新增的標籤原則。

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>後續步驟
* 定義原則規則 (如上述範例所示) 之後，您必須建立原則定義，並將它指派到某個範圍。 範圍可以是訂用帳戶、資源群組或資源。 如需建立和指派原則的範例，請參閱[指派和管理原則](resource-manager-policy-create-assign.md)。 
* 如需資源原則的簡介，請參閱[資源原則概觀](resource-manager-policy.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。


