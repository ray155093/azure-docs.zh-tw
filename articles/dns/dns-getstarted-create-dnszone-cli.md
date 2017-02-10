---
title: "使用 CLI 建立 DNS 區域 | Microsoft Docs"
description: "了解如何建立 Azure DNS 中的 DNS 區域。 這份逐步指南，將引導您使用 Azure CLI 來建立與管理第一個 DNS 區域。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>使用 CLI 建立 Azure DNS 區域

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文將逐步引導您使用跨平台 Azure CLI 此適用 Windows、Mac 和 Linux) 來建立 DNS 區域，。 您也可以使用 [Azure PowerShell](dns-getstarted-create-dnszone.md) 或 [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)來建立 DNS 區域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `azure network dns zone create` 命令建立 DNS 區域。 若要查看此命令的說明，請輸入 `azure network dns zone create -h`。

下列範例會在稱為 MyResourceGroup 的資源群組中建立稱為 contoso.com 的 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>確認您的 DNS 區域

### <a name="view-records"></a>檢視記錄

建立 DNS 區域也會建立下列 DNS 記錄：

* 「起始點授權」  (SOA) 記錄。 此記錄出現在每個 DNS 區域的根。
* 授權名稱伺服器 (NS) 記錄。 這些記錄顯示哪些名稱伺服器裝載該區域。 Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可能會指派至 Azure DNS 中的不同區域。 如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

若要檢視這些記錄，請使用 `azure network dns-record-set list`：

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> 位於 DNS 區域的根 (或「頂點」 ) 的記錄集使用 **@** 做為記錄集名稱。

### <a name="test-name-servers"></a>測試名稱伺服器

您可以使用 nslookup、dig 或 `Resolve-DnsName` PowerShell Cmdlet 等 DNS 工具測試您的 DNS 區域在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。 如 `azure network dns record-set list` 所提供，NS 記錄提供您區域的名稱伺服器。

下列範例使用 'dig'，使用指派給 DNS 區域的名稱伺服器來查詢網域 contoso.com。 請務必用您區域的正確值替代。

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
(1 server found)
global options: +cmd
  Got answer:
->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
  flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
  WARNING: recursion requested but not available

  OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 4000
  QUESTION SECTION:
contoso.com.                        IN      A

  AUTHORITY SECTION:
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>後續步驟

建立 DNS 區域後，請在您的區域中[建立 DNS 記錄集和記錄](dns-getstarted-create-recordset-cli.md)。




<!--HONumber=Jan17_HO4-->


