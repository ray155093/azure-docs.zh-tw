---
title: 使用 CLI 建立 DNS 區域的記錄集和記錄 | Microsoft Docs
description: 如何建立 Azure DNS 的主機記錄。使用 CLI 設定記錄集和記錄
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee

---
# 使用 CLI 建立 DNS 記錄集和記錄
> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)
> 
> 

本文將逐步引導您完成使用 CLI 建立記錄和記錄集的程序。建立 DNS 區域之後，您必須加入網域的 DNS 記錄。若要這樣做，您必須先了解 DNS 記錄和記錄集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## 建立記錄集和記錄
在本節中，我們將示範如何建立記錄集和記錄。在此範例中，您將會在 DNS 區域 "contoso.com" 中建立具有相對名稱 "www" 的記錄集。記錄的完整名稱是 "www.contoso.com"。記錄類型是 "A"，存留時間 (TTL) 是 60 秒。完成此步驟之後，您就能建立空的記錄集。

若要在區域頂點 (在此案例中為 "contoso.com") 建立記錄集，請使用記錄名稱 "@" (包括引號)。這是常見的 DNS 慣例。

### 1\.建立記錄集
若要建立記錄集，請使用 `azure network dns record-set create`。指定資源群組、區域名稱、記錄集相對名稱、記錄類型和 TTL。若未定義 `--ttl` 參數，預設值為四 (以秒為單位)。完成此步驟之後，您就具有空的 "www" 記錄集。

*使用方式：network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\.新增記錄
若要使用新建立的 "www" 記錄集，您必須對其新增記錄。您可以使用 `azure network dns record-set add-record` 將記錄新增至記錄集。

將記錄加入至記錄集的參數，根據記錄集的類型而所有不同。例如，使用 "A" 類型的記錄集時，您只能使用參數 `-a <IPv4 address>` 來指定記錄。

您可以使用下列命令將 IPv4 A記錄新增至 "www" 記錄集：

*使用方式：network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## 其他記錄類型範例
下列範例示範如何建立每一種記錄類型的記錄集。每個記錄集都會包含一筆記錄。

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## 後續步驟
若要管理您的記錄集和記錄，請參閱[使用 CLI 管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。

如需 Azure DNS 的詳細資訊，請參閱 [Azure DNS 概觀](dns-overview.md)。

<!---HONumber=AcomDC_1005_2016-->