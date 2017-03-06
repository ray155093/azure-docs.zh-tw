---
title: "使用 Azure CLI 1.0 管理 Azure 服務的反向 DNS 記錄 | Microsoft Docs"
description: "如何在資源管理員中使用 Azure CLI 1.0 管理 Azure 服務的反向 DNS 記錄或 PTR 記錄"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 227ede3445ced9cca71a3879afb0bb6c8c5c2482
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli-10"></a>如何使用 Azure CLI 1.0 管理 Azure 服務的反向 DNS 記錄

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

* [Azure CLI 1.0](dns-reverse-dns-record-operations-cli-nodejs.md) - 適用於傳統和資源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-reverse-dns-record-operations-cli.md) - 適用於資源管理部署模型的新一代 CLI。

## <a name="introduction"></a>簡介

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

如需傳統部署模型的詳細資訊，請參閱[如何使用 Azure PowerShell 管理 Azure 服務 (傳統) 的反向 DNS 記錄](dns-reverse-dns-record-operations-classic-ps.md)。


## <a name="validation-of-reverse-dns-records"></a>反向 DNS 記錄的驗證
為了確保其他人無法建立對應至您 DNS 網域的反向 DNS 記錄，Azure 僅會在下列其中一種情況成立時允許建立反向 DNS 記錄：

* "ReverseFqdn" 與其所指定之公用 IP 位址資源的 "Fqdn" 相同，或是相同訂用帳戶內任何公用 IP 位址的 "Fqdn"，例如，"ReverseFqdn" 為 "contosoapp1.northus.cloudapp.azure.com."。
* “ReverseFqdn” 會正向解析成它被指定的「公用 IP 位址」的名稱或 IP，或是相同訂用帳戶內的任何「公用 IP 位址」“Fqdn” 或 IP，例如 “ReverseFqdn” 是 “app1.contoso.com”。 這是 “contosoapp1.northus.cloudapp.azure.com” 的 CName 別名。

驗證檢查只會在設定或修改公用 IP 位址的反向 DNS 屬性時才會執行。 不會執行定期的重新驗證。

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>將反向 DNS 新增至現有的公用 IP 位址
您可以使用 azure network public-ip set，將反向 DNS 新增至現有的公用 IP 位址：

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

如果您想要將反向 DNS 新增至尚未有 DNS 名稱的現有公用 IP 位址，您也必須指定 DNS 名稱。 您可以使用 azure network public-ip set 來達到此目的：

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>建立具有反向 DNS 的公用 IP 位址
您可以透過使用 azure network public-ip create 指定的反向 DNS 屬性，建立新的公用 IP 位址：

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>檢視現有公用 IP 位址的反向 DNS
您可以使用 azure network public-ip show，檢視現有公用 IP 位址的設定值：

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>移除現有公用 IP 位址的反向 DNS
您可以使用 azure network public-ip set，移除現有公用 IP 位址的反向 DNS 屬性。 這可以透過將 ReverseFqdn 屬性值設定為空白來完成：

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


