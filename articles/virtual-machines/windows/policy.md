---
title: "在 Azure 中的 Windows VM 上以原則強制執行安全性 | Microsoft Docs"
description: "如何將原則套用至 Azure Resource Manager Windows 虛擬機器"
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
ms.date: 06/28/2017
ms.author: kasing
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 9874a825ea81ebb191710ebd46dceb70c1f20e60
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017


---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>使用 Azure Resource Manager 將原則套用至 Windows VM
藉由使用原則，組織可以強制執行整個企業的各種慣例和規則。 強制執行所要的行為有助於降低風險，同時促進組織的成功。 我們會在本文中說明如何使用 Azure Resource Manager 原則來為組織的虛擬機器定義您所要的行為。

如需原則的簡介，請參閱 [使用原則來管理資源和控制存取](../../azure-resource-manager/resource-manager-policy.md)。

## <a name="define-policy-for-permitted-virtual-machines"></a>針對允許的虛擬機器定義原則
若要確保您組織的虛擬機器與應用程式相容，您可以針對允許使用哪些作業系統設下限制。 您可以透過以下原則範例，允許只能建立 Windows Server 2012 R2 資料中心虛擬機器：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
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

使用萬用字元修改上述原則，來允許任何 Windows Server Datacenter 映像：

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

如需原則欄位的相關資訊，請參閱[原則別名](../../azure-resource-manager/resource-manager-policy.md#aliases)。

## <a name="define-policy-for-using-managed-disks"></a>定義使用受控磁碟的原則

若要要求使用受控磁碟，請使用以下原則：

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>後續步驟
* 定義原則規則 (如上述範例所示) 之後，您必須建立原則定義，並將它指派到某個範圍。 範圍可以是訂用帳戶、資源群組或資源。 若要透過入口網站來指派原則，請參閱[使用 Azure 入口網站來指派和管理資源原則](../../azure-resource-manager/resource-manager-policy-portal.md)。 若要透過 REST API、PowerShell 或 Azure CLI 來指派原則，請參閱[透過指令碼來指派和管理原則](../../azure-resource-manager/resource-manager-policy-create-assign.md)。
* 如需資源原則的簡介，請參閱[資源原則概觀](../../azure-resource-manager/resource-manager-policy.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](../../azure-resource-manager/resource-manager-subscription-governance.md)。

