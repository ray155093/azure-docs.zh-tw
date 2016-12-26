---
title: "使用 CLI 建立 DNS 區域的記錄集和記錄 | Microsoft Docs"
description: "如何建立 Azure DNS 的主機記錄。使用 CLI 設定記錄集和記錄"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: e377f176fe24a8e7e42d409f86d6b0093ce5e7c4

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>使用 CLI 建立 DNS 記錄集和記錄

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

本文將逐步引導您完成使用 CLI 建立記錄和記錄集的程序。 若要這樣做，您必須先了解 DNS 記錄和記錄集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

本節說明如何在 Azure DNS 中建立 DNS 記錄。 此範例假設您已經有[安裝 Azure CLI、登入，並建立 DNS 區域](dns-getstarted-create-dnszone-cli.md)。

在此頁面上所有範例都使用 'A' DNS 記錄類型。 如需其他記錄類型與如何管理 DNS 記錄與記錄集的進一步詳細資訊，請參閱[使用 Azure CLI 來管理 DNS 記錄與記錄集](dns-operations-recordsets-cli.md)。

## <a name="create-a-record-set-and-record"></a>建立記錄集和記錄

在本節中，我們將示範如何建立記錄集和記錄。 在此範例中，您將會在 DNS 區域 "contoso.com" 中建立具有相對名稱 "www" 的記錄集。 記錄的完整名稱是 "www.contoso.com"。 記錄類型是 "A"，存留時間 (TTL) 是 60 秒。 完成此步驟之後，您就能建立空的記錄集。

若要在區域頂點 (在此案例中為 "contoso.com") 建立記錄集，請使用記錄名稱 "@", (包括引號)。 這是常見的 DNS 慣例。

### <a name="1-create-a-record-set"></a>1.建立記錄集

如果新記錄的名稱和類型與現有記錄相同，您需要將它新增至現有的記錄集。 您可以略過此步驟，並跳到下方的[新增記錄](#add-records)。 否則，如果新記錄的名稱和類型與現有記錄不同，則必須建立新的記錄集。

您可以使用 `azure network dns record-set create` 命令來建立記錄集。 如需協助，請參閱 `azure network dns record-set create -h`。  

建立記錄集時，您必須指定記錄集名稱、區域、存留時間 (TTL) 和記錄類型。 

```azurecli
azure network dns record-set create myresourcegroup contoso.com www A 60
```

完成此步驟之後，您就具有空的 "www" 記錄集。 若要使用新建立的 "www" 記錄集，您必須對其新增記錄。

### <a name="2-add-records"></a>2.新增記錄

您可以使用 `azure network dns record-set add-record`將記錄新增至記錄集。 如需協助，請參閱 `azure network dns record-set add-record -h`。

將記錄加入至記錄集的參數，根據記錄集的類型而所有不同。 例如，使用 "A" 類型的記錄集時，只能使用 `-a <IPv4 address>` 參數來指定記錄。 請參閱 `azure network dns record-set add-record -h` 來列出其他記錄類型的參數。

您可以使用下列命令將 A 記錄新增至 "www" 記錄集：

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 1.2.3.4
```

### <a name="verify-name-resolution"></a>驗證名稱解析

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具測試您的 DNS 記錄在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將[ DNS 查詢直接導向您區域的其中一個名稱伺服器](dns-getstarted-create-dnszone.md#test-name-servers)。 請務必在下列命令中用正確的值取代您的記錄。

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>後續步驟

了解如何[將網域名稱委派給 Azure DNS 名稱伺服器](dns-domain-delegation.md)

了解如何[使用 Azure CLI 管理現有的 DNS 區域](dns-operations-dnszones-cli.md)。

了解如何[使用 Azure CLI 管理 DNS 記錄和記錄集](dns-operations-recordsets-cli.md)。




<!--HONumber=Dec16_HO2-->


