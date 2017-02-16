---
title: "使用 PowerShell 管理 Azure DNS 中的反向 DNS 記錄 | Microsoft Docs"
description: "Azure DNS 可讓您在資源管理員中使用 PowerShell 管理 Azure 服務的反向 DNS 記錄或 PTR 記錄"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: b95703c5-e94e-4009-ab37-0c3f7908783c
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: efa52b5f30cab16bfde4202dbfe2c95f4464e2c4
ms.openlocfilehash: 730321ccacb211ec82e69f8ebc69ee84cbf46a01


---
# <a name="manage-reverse-dns-records-for-your-azure-services-using-powershell"></a>使用 PowerShell 管理 Azure 服務的反向 DNS 記錄

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

如需傳統部署模型的詳細資訊，請參閱[如何使用 Azure PowerShell 管理 Azure 服務 (傳統) 的反向 DNS 記錄](dns-reverse-dns-record-operations-classic-ps.md)。

## <a name="validation-of-reverse-dns-records"></a>反向 DNS 記錄的驗證
為了確保其他人無法建立對應至您 DNS 網域的反向 DNS 記錄，Azure 僅會在下列其中一種情況成立時允許建立反向 DNS 記錄：

* "ReverseFqdn" 與其所指定之公用 IP 位址資源的 "Fqdn" 相同，或是相同訂用帳戶內任何公用 IP 位址的 "Fqdn"，例如，"ReverseFqdn" 為 "contosoapp1.northus.cloudapp.azure.com."。
* “ReverseFqdn” 會正向解析成它被指定的「公用 IP 位址」的名稱或 IP，或是相同訂用帳戶內的任何「公用 IP 位址」“Fqdn” 或 IP，例如 “ReverseFqdn” 是 “app1.contoso.com”。 這是 “contosoapp1.northus.cloudapp.azure.com” 的 CName 別名。

驗證檢查只會在設定或修改公用 IP 位址的反向 DNS 屬性時才會執行。 不會執行定期的重新驗證。

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>將反向 DNS 新增至現有的公用 IP 位址
您可以使用 "Set-AzureRmPublicIpAddress" Cmdlet，將反向 DNS 新增至現有的公用 IP 位址：

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

如果您想要將反向 DNS 新增至尚未有 DNS 名稱的現有公用 IP 位址，您也必須指定 DNS 名稱。 您可以使用 “Set-AzureRmPublicIpAddress” Cmdlet 來達成此目的︰

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings
    PS C:\> $pip.DnsSettings.DomainNameLabel = "contosoapp1"
    PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

## <a name="create-a-public-ip-address-with-reverse-dns"></a>建立具有反向 DNS 的公用 IP 位址
您可以透過使用 “New-AzureRmPublicIpAddress” 指定的反向 DNS 屬性，建立新的公用 IP 位址：

    PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>檢視現有公用 IP 位址的反向 DNS
您可以使用 "Get-AzureRmPublicIpAddress" Cmdlet，檢視現有公用 IP 位址的設定值：

    PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>移除現有公用 IP 位址的反向 DNS
您可以使用 "Set-AzureRmPublicIpAddress" Cmdlet，移除現有公用 IP 位址的反向 DNS 屬性。 這可以透過將 ReverseFqdn 屬性值設定為空白來完成：

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = ""
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip


[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]




<!--HONumber=Jan17_HO4-->


