---
title: "儲存體的 Azure Resource Manager 原則 | Microsoft Docs"
description: "描述 Azure Resource Manager 管理儲存體帳戶部署的的原則。"
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
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>將 Azure 資源原則套用至儲存體帳戶
透過 Azure Resource Manager 原則，您可以為組織如何部署資源定義一致的規則。 您可以建立自訂的原則，確保組織中的使用者不會違反管理組織資源所需的慣例。 本主題說明定義 Azure 儲存體帳戶規則的幾個原則。 如需有關原則的詳細資訊，請參閱[使用資源原則來管理資源](resource-manager-policy.md)。

本主題中的範例說明原則規則中的硬式編碼值。 不過，您可以在指派原則時使用參數來傳入所使用的值。 如需詳細資訊，請參閱[原則參數](resource-manager-policy.md#parameters)。

## <a name="define-permitted-storage-account-types"></a>定義允許的儲存體帳戶類型

下列原則會限制可部署的[儲存體帳戶類型](../storage/storage-redundancy.md)︰

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="define-permitted-access-tier"></a>定義允許的存取層

下列原則指定可針對儲存體帳戶指定的[存取層](../storage/storage-blob-storage-tiers.md)類型︰

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>請確定已啟用加密

下列原則要求所有儲存體帳戶啟用[儲存體服務加密](../storage/storage-service-encryption.md)：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="create-and-assign-policies"></a>建立並指派原則

定義原則規則 (如上述範例所示) 之後，您必須建立原則，並將它指派到某個範圍。 範圍可以是訂用帳戶、資源群組或資源。 如需建立和指派原則的範例，請參閱[建立並指派原則](resource-manager-policy.md#create-and-assign-a-policy)。 

## <a name="next-steps"></a>後續步驟
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。




<!--HONumber=Jan17_HO3-->


