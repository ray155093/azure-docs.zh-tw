---
title: "使用 Azure CLI 2.0 建立 DNS 記錄 | Microsoft Docs"
description: "如何建立 Azure DNS 的主機記錄。使用 Azure CLI 2.0 設定記錄集和記錄"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: ca713647a9f795d7803c32eac7fba9b5b6ac95cf
ms.lasthandoff: 03/01/2017

---

# <a name="how-to-create-dns-records-using-the-azure-cli-20"></a>如何使用 Azure CLI 2.0 建立 DNS 記錄

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

本文將逐步引導您完成使用 Azure CLI 建立記錄和記錄集的程序。

## <a name="introduction"></a>簡介

在 Azure DNS 中建立 DNS 記錄前，您需要先了解 Azure DNS 如何將 DNS 記錄組織成 DNS 記錄集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

如需在 Azure DNS 的 DNS 記錄的詳細資訊，請參閱 [DNS 區域與記錄](dns-zones-records.md)。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本

您可以使用下列其中一個 CLI 版本來完成工作︰

* [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md) - 適用於傳統和資源管理部署模型的 CLI。
* [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md) - 適用於資源管理部署模型的新一代 CLI。

## <a name="create-a-record-set-and-record"></a>建立記錄集和記錄

本節說明如何在 Azure DNS 中建立 DNS 記錄。 此範例假設您已經有[安裝 Azure CLI 2.0、登入，並建立 DNS 區域](dns-getstarted-create-dnszone-cli.md)。

在此頁面上所有範例都使用 'A' DNS 記錄類型。 如需其他記錄類型與如何管理 DNS 記錄與記錄集的進一步詳細資訊，請參閱[使用 Azure CLI 2.0 來管理 DNS 記錄與記錄集](dns-operations-recordsets-cli.md)。

## <a name="create-a-dns-record"></a>建立 DNS 記錄

若要建立 DNS 記錄，請使用 `az network dns record-set ? add` 命令 (其中 ? 是記錄類型)。 如需協助，請參閱 `az network dns record-set --help`。

建立記錄時，您必須指定資源群組名稱、區域名稱、記錄集名稱、記錄類型，以及所建立記錄的詳細資料。

如果記錄集不存在，此命令會為您建立。 如果記錄集已經存在，此命令會將您指定的記錄新增至現有的記錄集。 

如果建立新的記錄集，則會使用預設存留時間 (TTL) 3600。 如需有關如何使用不同 TTL 的指示，請參閱[使用 Azure CLI 2.0 在 Azure DNS 中管理 DNS 記錄](dns-operations-recordsets-cli.md)。

下列範例會在 MyResourceGroup 資源群組的 contoso.com 區域中建立稱為 www 的 A 記錄。 A 記錄的 IP 位址是&1;.2.3.4。

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

若要在區域頂點 (在此案例中為 "contoso.com") 建立記錄集，請使用記錄名稱 "@" (包括引號)：

```azurecli
az network dns record-set a --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

用來指定記錄資料的參數，根據記錄的類型而所有不同。 例如，對於類型 "A" 的記錄，您可使用參數 `--ipv4-address <IPv4 address>` 指定 IPv4 位址。 請參閱 `az network dns record --help` 來列出其他記錄類型的參數。 如需每種記錄類型的範例，請參閱[使用 Azure CLI 2.0 管理 DNS 記錄和記錄集](dns-operations-recordsets-cli.md)。


## <a name="verify-name-resolution"></a>驗證名稱解析

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具測試您的 DNS 記錄在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將[ DNS 查詢直接導向您區域的其中一個名稱伺服器](dns-getstarted-create-dnszone.md#test-name-servers)。 請務必在下列命令中用正確的值取代您的記錄。

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>後續步驟

了解如何[將網域名稱委派給 Azure DNS 名稱伺服器](dns-domain-delegation.md)

了解如何[使用 Azure CLI 2.0 管理 DNS 區域](dns-operations-dnszones-cli.md)。

了解如何[使用 Azure CLI 2.0 管理 DNS 記錄和記錄集](dns-operations-recordsets-cli.md)。


