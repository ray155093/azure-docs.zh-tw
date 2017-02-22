---
title: "使用 PowerShell 建立 DNS 區域的記錄集和記錄 | Microsoft Docs"
description: "如何建立 Azure DNS 的主機記錄。 使用 PowerShell 建立記錄集和記錄"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f4c17d03ff637659a7bc7cde378878d8a4827b80
ms.openlocfilehash: 175e8620828a2b0a0aff6de0b1a39860ea59514b

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>使用 PowerShell 建立 DNS 記錄集和記錄

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

本文將逐步引導您完成透過 Azure PowerShell 建立記錄和記錄集的程序。

## <a name="introduction"></a>簡介

在 Azure DNS 中建立 DNS 記錄前，您需要先了解 Azure DNS 如何將 DNS 記錄組織成 DNS 記錄集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

如需在 Azure DNS 的 DNS 記錄的詳細資訊，請參閱 [DNS 區域與記錄](dns-zones-records.md)。

## <a name="create-a-record-set-and-record"></a>建立記錄集和記錄

本節說明如何在 Azure DNS 中建立 DNS 記錄。 此範例假設您已[安裝 Azure PowerShell、登入，並建立 DNS 區域](dns-getstarted-create-dnszone.md)。

在此頁面上所有範例都使用 'A' DNS 記錄類型。 如需其他記錄類型與如何管理 DNS 記錄與記錄集的進一步詳細資訊，請參閱[使用 PowerShell 來管理 DNS 記錄與記錄集](dns-operations-recordsets.md)。

如果新記錄的名稱和類型與現有記錄相同，您需要[將它新增至現有的記錄集](#add-a-record-to-an-existing-record-set)。 否則，如果新記錄的名稱和類型與現有記錄不同，則必須[建立新的記錄集](#create-records-in-a-new-record-set)。 

### <a name="create-records-in-a-new-record-set"></a>在新記錄集中建立記錄

您可以使用 `New-AzureRmDnsRecordSet` Cmdlet 來建立記錄集。 建立記錄集時，您必須指定記錄集名稱、區域、存留時間 (TTL) 和記錄類型，與要建立的記錄。

若要在區域頂點 (在此案例中為 "contoso.com") 建立記錄集，請使用記錄名稱 "@", (包括引號)。 這是常見的 DNS 慣例。

下列範例會在 DNS 區域 "contoso.com" 中建立具有相對名稱 "www" 的新記錄集。 記錄集的完整名稱是 "www.contoso.com"。 記錄類型為 "A"，且 TTL 為 3600 秒。 此記錄集都會包含一筆記錄，其中 IP 位址為 "1.2.3.4"

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

如果您需要建立新的記錄集，其中包含多筆記錄，您需要先建立本機陣列，其中包含要新增的記錄。  此項目會如下所示，傳至 `New-AzureRmDnsRecordSet`：

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>將記錄新增至現有的記錄集

若要將記錄新增至現有的記錄集，請遵循下列三個步驟︰

1. 取得現有記錄集

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. 將新記錄新增至本機記錄集。 這是一種離線作業。

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. 將對變更的認可傳回給 Azure DNS 服務 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>驗證名稱解析

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具測試您的 DNS 記錄在 Azure DNS 名稱伺服器上是否存在。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將[ DNS 查詢直接導向您區域的其中一個名稱伺服器](dns-getstarted-create-dnszone.md#test-name-servers)。 請務必在下列範例中用正確的值取代您的記錄區域：

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

了解如何[使用 PowerShell 管理 DNS 區域](dns-operations-dnszones.md)。

了解如何[使用 PowerShell 管理 DNS 記錄和記錄集](dns-operations-recordsets.md)。





<!--HONumber=Dec16_HO3-->


