---
title: "使用 PowerShell 管理傳統 Azure 服務的反向 DNS 記錄 | Microsoft Docs"
description: "Azure DNS 可讓您在傳統部署模型中使用 PowerShell 管理 Azure 服務的反向 DNS 記錄或 PTR 記錄。"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 399cfcad5c17cdb12a4063e11e1ff353e88e56a7
ms.lasthandoff: 02/10/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>如何使用 Azure PowerShell 管理 Azure 服務 (傳統) 的反向 DNS 記錄

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 了解如何[使用 Resource Manager 模型執行這些步驟](dns-reverse-dns-record-operations-ps.md)。

## <a name="validation-of-reverse-dns-records"></a>反向 DNS 記錄的驗證
為了確保其他人無法建立對應至您 DNS 網域的反向 DNS 記錄，Azure 僅會在下列其中一種情況成立時允許建立反向 DNS 記錄：

* 反向 DNS FQDN 為其所指定之「雲端服務」的名稱，或是相同訂用帳戶內的任何「雲端服務」名稱，例如，例如，反向 DNS 為 "contosoapp1.cloudapp.net"。
* 反向 DNS FQDN 正向解析為其所指定之「雲端服務」的名稱或 IP，或是相同訂用帳戶內任何「雲端服務」的名稱或 IP，例如，反向 DNS 為 "app1.contoso.com"，這是 contosoapp1.cloudapp.net  的 CName 別名。

驗證檢查只會在設定或修改「雲端服務」的反向 DNS 屬性時才會執行。 不會執行定期的重新驗證。

## <a name="add-reverse-dns-to-existing-cloud-services"></a>將反向 DNS 新增至現有的雲端服務
您可以使用 "Set-AzureService" Cmdlet，將反向 DNS 記錄新增至現有的雲端服務：

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

## <a name="create-a-cloud-service-with-reverse-dns"></a>建立具有反向 DNS 的雲端服務
您可以使用 "Set-AzureService" Cmdlet，建立指定了反向 DNS 屬性的新雲端服務：

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

## <a name="view-reverse-dns-for-existing-cloud-services"></a>檢視現有雲端服務的反向 DNS
您可以使用 "Get-AzureService" Cmdlet，檢視現有雲端服務的設定值：

```powershell
Get-AzureService "contosoapp1"
```

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>移除現有雲端服務的反向 DNS
您可以使用 "Set-AzureService" Cmdlet，移除現有雲端服務的反向 DNS 屬性。 這可以透過將反向 DNS 屬性值設定為空白來完成：

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]


