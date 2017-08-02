---
title: "Azure 資源原則產生的 RequestDisallowedByPolicy 錯誤 | Microsoft Docs"
description: "描述 RequestDisallowedByPolicy 錯誤的原因。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 182a27e444c2f5db66d518a1a0c608d3e319d553
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 資源原則產生的 RequestDisallowedByPolicy 錯誤

本文說明 RequestDisallowedByPolicy 錯誤的原因，其中也會提供此錯誤的解決方案。

## <a name="symptom"></a>徵狀

當您嘗試在部署期間採取動作時，可能會收到 **RequestDisallowedByPolicy** 錯誤來阻止執行該動作。 以下是一個錯誤範例：

```
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>疑難排解

若要擷取有關封鎖了您部署之原則的詳細資訊，請使用下列其中一種方法：

### <a name="method-1"></a>方法 1

在 PowerShell 中，提供該原則識別碼作為 **Id** 參數，以擷取有關封鎖了您部署之原則的詳細資料。

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>方法 2 

在 Azure CLI 2.0 中，提供原則定義的名稱： 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>方案

基於安全性或合規性等因素，您的 IT 部門可能會強制執行資源原則，以禁止建立公用 IP 位址、網路安全性群組、使用者定義的路由或路由表。 在＜徵狀＞一節所述的錯誤訊息範例中，已將原則命名為 **regionPolicyDefinition**，但也可能不一樣。
若要解決這個問題，請與您的 IT 部門合作來檢閱資源原則，並決定如何依照這些原則來執行所要求的動作。


如需詳細資訊，請參閱下列文章。

- [資源原則概觀](resource-manager-policy.md)
- [常見的部署錯誤：RequestDisallowedByPolicy](resource-manager-common-deployment-errors.md#requestdisallowedbypolicy)

 



