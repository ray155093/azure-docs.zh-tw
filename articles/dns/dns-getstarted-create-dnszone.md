---
title: "開始使用 Azure DNS | Microsoft Docs"
description: "了解如何建立 Azure DNS 中的 DNS 區域。 這份逐步指南將引導您使用 Azure PowerShell 來建立與管理第一個 DNS 區域。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 798717eca5180e445b14351bbfc694ed206ee9dc
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-using-powershell"></a>使用 PowerShell 建立 DNS 區域

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

本文將逐步引導您完成使用 Azure PowerShell 建立 DNS 區域的步驟。 您也可以使用跨平台 [Azure CLI](dns-getstarted-create-dnszone-cli.md) 或 [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)來建立 DNS 區域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `New-AzureRmDnsZone` Cmdlet 建立 DNS 區域。 下列範例會在稱為 MyResourceGroup 的資源群組中建立稱為 contoso.com 的 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="verify-your-dns-zone"></a>確認您的 DNS 區域

### <a name="view-records"></a>檢視記錄

建立 DNS 區域也會建立下列 DNS 記錄：

* 「起始點授權」  (SOA) 記錄。 此記錄出現在每個 DNS 區域的根。
* 授權名稱伺服器 (NS) 記錄。 這些記錄顯示哪些名稱伺服器裝載該區域。 Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可能會指派至 Azure DNS 中的不同區域。 如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

若要檢視這些記錄，請使用 `Get-AzureRmDnsRecordSet`：

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 172800
Etag              : f573237b-088c-424a-b53c-08567d87d049
RecordType        : NS
Records           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
Metadata          : 

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 3600
Etag              : bf88a27d-0eec-4847-ad42-f0c83b9a2c32
RecordType        : SOA
Records           : {[ns1-01.azure-dns.com.,azuredns-hostmaster.microsoft.com,3600,300,2419200,300]}
Metadata          : 
```

> [!NOTE]
> 位於 DNS 區域的根 (或「頂點」 ) 的記錄集使用 **@** 做為記錄集名稱。

### <a name="test-name-servers"></a>測試名稱伺服器

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具測試您的 DNS 區域在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。 如上面的 `Get-AzureRmDnsRecordSet` 所列，NS 記錄提供您區域的名稱伺服器。 請務必在下列範例中用正確的值取代您的區域：

```
nslookup
> set type=SOA
> server ns1-01.azure-dns.com
> contoso.com

Server: ns1-01.azure-dns.com
Address:  40.90.4.1

contoso.com
        primary name server = ns1-01.azure-dns.com
        responsible mail addr = azuredns-hostmaster.microsoft.com
        serial  = 1
        refresh = 3600 (1 hour)
        retry   = 300 (5 mins)
        expire  = 2419200 (28 days)
        default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>後續步驟

建立 DNS 區域後，請建立 [記錄集和記錄](dns-getstarted-create-recordset.md)，以建立適用於您網際網路網域的 DNS 記錄。

