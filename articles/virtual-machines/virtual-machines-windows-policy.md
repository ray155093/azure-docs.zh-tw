---
title: "在 Azure 中的 Windows VM 上以原則強制執行安全性 | Microsoft Docs"
description: "如何將原則套用至 Azure Resource Manager  Windows 虛擬機器"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/13/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 652a8a0dcb3216e9b762b85de56673beda74edc4
ms.openlocfilehash: 83470aaf607a666a63bae5b9897d0b273e327377


---
# <a name="apply-security-and-policies-to-windows-vms-with-azure-resource-manager"></a>使用 Azure Resource Manager 將安全性和原則套用至 Windows VM
藉由使用原則，組織可以強制執行整個企業的各種慣例和規則。 強制執行所要的行為有助於降低風險，同時促進組織的成功。 在本文中，我們將說明如何使用 Azure Resource Manager 原則來定義您組織虛擬機器所要的行為。

完成此作業的步驟大致如下

1. Azure Resource Manager 原則入門
2. 定義虛擬機器的原則
3. 建立原則
4. 套用原則

## <a name="azure-resource-manager-policy-101"></a>Azure Resource Manager 原則入門
若要開始使用 Azure Resource Manager 原則，建議閱讀下文，然後繼續進行本文中的步驟。 下文描述原則的基本定義和結構、如何評估原則，並提供原則定義的各種範例。

* [使用原則來管理資源和控制存取](../azure-resource-manager/resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>定義虛擬機器的原則
其中一個常見的企業案例可能是只允許其使用者從特定作業系統建立虛擬機器，而這些作業系統已經測試可與 LOB 應用程式相容。 使用 Azure Resource Manager 原則可以透過幾個步驟完成這項工作。
在此原則範例中，我們將只允許建立 Windows Server 2012 R2 Datacenter 虛擬機器。 原則定義如下所示。

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

使用下面的變更，可以針對下列案例輕鬆地修改上述原則：您可能要允許任何要用於虛擬機器部署的 Windows Server Datacenter 映像。

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>虛擬機器屬性欄位
下表描述可用作您原則定義中欄位的虛擬機器屬性。 如需原則欄位的詳細資訊，請參閱下文︰

* [欄位和來源](../azure-resource-manager/resource-manager-policy.md#conditions)

| 欄位名稱 | 說明 |
| --- | --- |
| imagePublisher |指定映像的發行者 |
| imageOffer |指定所選擇映像發行者的優惠 |
| imageSku |指定所選擇優惠的 SKU |
| imageVersion |指定所選擇 SKU 的映像版本 |

## <a name="create-the-policy"></a>建立原則
直接使用 REST API 或 PowerShell Cmdlet 可以輕鬆地建立原則。 如需建立原則的詳細資訊，請參閱下文︰

* [建立原則](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)

## <a name="apply-the-policy"></a>套用原則
建立原則之後，您需要為定義的範圍加以套用。 範圍可以是訂用帳戶、資源群組，甚至是資源。 如需套用原則的詳細資訊，請參閱下文︰

* [建立原則](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)



<!--HONumber=Jan17_HO4-->


