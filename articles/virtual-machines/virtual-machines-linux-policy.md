<properties
	pageTitle="將原則套用至 Azure Resource Manager 虛擬機器 | Microsoft Azure"
	description="如何將原則套用至 Azure Resource Manager Linux 虛擬機器"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="singhkay"/>

# 將原則套用至 Azure Resource Manager 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型

藉由使用原則，組織可以強制執行整個企業的各種慣例和規則。強制執行所要的行為有助於降低風險，同時促進組織的成功。在本文中，我們將說明如何使用 Azure Resource Manager 原則來定義您組織虛擬機器所要的行為。

完成此作業的步驟大致如下

1. Azure Resource Manager 原則入門
2. 定義虛擬機器的原則
3. 建立原則
4. 套用原則

## Azure Resource Manager 原則入門

若要開始使用 Azure Resource Manager 原則，建議閱讀下文，然後繼續進行本文中的步驟。下文描述原則的基本定義和結構、如何評估原則，並提供原則定義的各種範例。

* [使用原則來管理資源和控制存取](../resource-manager-policy.md)

## 定義虛擬機器的原則

其中一個常見的企業案例可能是只允許其使用者從特定作業系統建立虛擬機器，而這些作業系統已經測試可與 LOB 應用程式相容。使用 Azure Resource Manager 原則可以透過幾個步驟完成這項工作。在此原則範例中，我們將只允許建立 Ubuntu 14.04.2-LTS 虛擬機器。原則定義如下所示。

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
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
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

使用下面的變更，可以針對下列案例輕鬆地修改上述原則：您可能要允許任何要用於虛擬機器部署的 Ubuntu LTS 映像。

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### 虛擬機器屬性欄位

下表描述可用作您原則定義中欄位的虛擬機器屬性。如需原則欄位的詳細資訊，請參閱下文︰

* [欄位和來源](../resource-manager-policy.md#fields-and-sources)


| 欄位名稱 | 說明 |
|----------------|----------------------------------------------------|
| imagePublisher | 指定映像的發行者 |
| imageOffer | 指定所選擇映像發行者的優惠 |
| imageSku | 指定所選擇優惠的 SKU |
| imageVersion | 指定所選擇 SKU 的映像版本 |

## 建立原則

直接使用 REST API 或 PowerShell Cmdlet 可以輕鬆地建立原則。如需建立原則的詳細資訊，請參閱下文︰

* [建立原則](../resource-manager-policy.md#creating-a-policy)


## 套用原則

建立原則之後，您需要為定義的範圍加以套用。範圍可以是訂用帳戶、資源群組甚至資源。如需套用原則的詳細資訊，請參閱下文︰

* [建立原則](../resource-manager-policy.md#applying-a-policy)

<!---HONumber=AcomDC_0420_2016-->