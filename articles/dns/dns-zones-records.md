---
title: "DNS 區域和記錄 | Microsoft Docs"
description: "將 DNS 區域和記錄，裝載於 Microsoft Azure DNS 中的支援概觀。"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: jtuliani
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: b99bc20b66787c7a87fa49143b4cbf861e0e68a2

---

# <a name="dns-zones-and-records"></a>DNS 區域和記錄

此頁面說明的重要概念，包括網域、DNS 區域、DNS 記錄和記錄集，以及在 Azure DNS 中所受到的支援。

## <a name="domain-names"></a>網域名稱

網域名稱系統是網域階層。 階層從「根」網域開始，其名稱只是 '**.**'。  下面接著最上層網域，例如 'com'、'net'、'org'、'uk' 或 'jp'。  再往下是第二層網域，例如 'org.uk' 或 'co.jp'。 DNS 階層中的網域散佈在全球，裝載在世界各地的 DNS 名稱伺服器上。

網域名稱註冊機構是種組織，可讓您購買網域名稱，例如 'contoso.com'。  購買網域名稱可讓您控制該名稱底下的 DNS 階層，例如將網域名稱 'www.contoso.com' 導向貴公司的網站。 可以讓該註冊機構代表您，將網域裝載在其自有的名稱伺服器上；您也可以自行指定替代的名稱伺服器。

Azure DNS 提供散佈全球、高可用性的名稱伺服器基礎結構，可用來裝載您的網域。 只要將您的網域裝載於 Azure DNS 中，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具、計費方式和支援來管理 DNS 記錄。

Azure DNS 目前不支援購買網域名稱。 若想要購買網域名稱，必須洽詢第三方網域名稱註冊機構。 註冊機構通常會收取些微年費。 然後便可以在 Azure DNS 裝載這些網域來管理 DNS 記錄。 如需詳細資訊，請參閱 [將網域委派給 Azure DNS](dns-domain-delegation.md) 。

## <a name="dns-zones"></a>DNS 區域

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。

例如，網域 'contoso.com' 可能包含數筆 DNS 記錄，例如 'mail.contoso.com' (用於郵件伺服器) 和 'www.contoso.com' (用於網站)。

在 Azure DNS 中建立 DNS 區域時，區域的名稱在資源群組內必須是唯一的。 不同的資源群組或不同的 Azure 訂用帳戶中，可重複使用相同的區域名稱。 多個區域共用相同的名稱時，系統就會將不同的名稱伺服器位址，指派給每個執行個體。 只可以向網域名稱註冊機構登記一組位址。

> [!NOTE]
> 您不必擁有網域名稱，也能在 Azure DNS 中以該網域名稱建立 DNS 區域。 不過，的確需要擁有網域，才能將 Azure DNS 名稱伺服器，設定為網域名稱的正確名稱伺服器，供網域名稱註冊機構使用。

如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

## <a name="dns-records"></a>DNS 記錄

### <a name="record-types"></a>記錄類型

每一筆 DNS 記錄都有名稱和類型。 記錄根據所包含的資料而組織成各種類型。 最常見的類型為 'A' 記錄，可將名稱對應到 IPv4 位址。 另一個一般類型為 'MX' 記錄，可將名稱對應到郵件伺服器。

Azure DNS 支援所有常見的 DNS 記錄類型：A、AAAA、CNAME、MX、NS、PTR、SOA、SRV 和 TXT。

### <a name="record-names"></a>記錄名稱

在 Azure DNS 中，記錄是使用相對名稱來指定。 「完整」網域名稱 (FQDN) 包含區域名稱，而「相對」名稱不含區域名稱。 比方說，區域 'contoso.com' 中的相對記錄名稱 'www' 就給出完整記錄名稱 'www.contoso.com'。

「頂點」記錄是 DNS 區域根目錄 (或「頂點」) 的 DNS 記錄。 例如，在 DNS 區域 'contoso.com' 中，頂點記錄也具有 'contoso.com' 的完整名稱 (這有時候稱為「裸」網域)。  依照慣例，相對名稱 '@' 會用來建立頂點記錄。

### <a name="record-sets"></a>記錄集

有時候，您需要以指定的名稱和類型建立多筆 DNS 記錄。 例如，假設 'www.contoso.com' 網站裝載於兩個不同的 IP 位址。 網站需要兩筆不同的 A 記錄，每個 IP 位址各一筆。 這是記錄集的範例。

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS 使用「記錄集」來管理 DNS 記錄。 記錄集 (也稱為「資源」記錄集) 是指一個區域中，具有相同名稱和相同類型的 DNS 記錄集合。 大部分的記錄集只包含單一記錄，但像是記錄集包含多筆記錄的例子也屢見不鮮。

例如，假設您已經在區域 'contoso.com' 中建立了 A 記錄 'www'，指向 IP 位址 '134.170.185.46' （上述第一項記錄）。  若要建立第二筆記錄，您會將該記錄新增至現有的記錄集，而非建立新的記錄集。

SOA 和 CNAME 記錄類型是例外狀況。 對於這些類型，DNS 標準不允許具有相同名稱的多筆記錄，因此這些記錄集只能包含單一記錄。

### <a name="time-to-live"></a>存留時間

存留時間，或 TTL，指定每一筆記錄由用戶端快取多久，之後才會重新查詢。 在上述範例中，TTL 是 3600 秒 (1 小時)。

在 Azure DNS 中，TTL 是針對記錄集而指定，而非針對每一筆記錄，因此相同的值，會套用到該記錄集內的所有記錄。  您可以指定介於 1 到 2,147,483,647 秒之間的任何 TTL 值。

### <a name="wildcard-records"></a>萬用字元記錄

Azure DNS 支援 [萬用字元記錄](https://en.wikipedia.org/wiki/Wildcard_DNS_record)。 萬用字元記錄會傳回具有相符名稱的任何查詢 (除非在非萬用字元記錄集內，還有更接近的相符項目)。 針對所有記錄類型支援 NS 和 SOA 以外的所有萬用字元記錄集。

若要建立萬用字元記錄集，請使用記錄集名稱 '\*'。 或者，也可以使用最左邊標籤是 '\*' 的名稱，例如 '\*.foo'。

### <a name="cname-records"></a>CNAME 記錄

CNAME 記錄集不能與其他具有相同名稱的記錄集共存。 例如，您無法同時建立具有相對名稱 'www' 的 CNAME 記錄集和具有相對名稱 'www' 的 A 記錄。

因為區域頂點 (名稱 = '@')) 一定會包含建立區域時所建立的 NS 和 SOA 記錄集，您無法在區域頂點建立 CNAME 記錄集。

這些條件約束源自於 DNS 標準，而不是 Azure DNS 的限制。

### <a name="ns-records"></a>NS 記錄

在每個區域頂點 (名稱 = '@'),) 會自動建立 NS 記錄集，並在刪除該區域時自動將其刪除 (無法個別刪除)。  可以修改此記錄集的 TTL，但無法修改記錄，因為依照預先設定，記錄會參考指派給該區域的 Azure DNS 名稱伺服器。

在該區域內可以建立和刪除其他 NS 記錄，在區域頂點則不可。  這可讓您設定子區域 (請參閱[在 Azure DNS 中委派子網域](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)。)

### <a name="soa-records"></a>SOA 記錄

在每個區域頂點 (名稱 = '@'),) 會自動建立 SOA 記錄集，並在刪除該區域時自動將其刪除。  無法個別建立或刪除 SOA 記錄。

可以修改 SOA 記錄除了 'Host' 屬性以外的所有屬性，因為依照預先設定，該屬性會參考 Azure DNS 所提供的主要名稱伺服器名稱。

### <a name="spf-records"></a>SPF 記錄

寄件者原則架構 (SPF) 記錄，是用來指定可以代表指定的網域名稱，傳送電子郵件的電子郵件伺服器。  請務必正確設定 SPF 記錄，以防止收件者將您的電子郵件標示為垃圾郵件。

DNS RFC 原本推出了新 'SPF' 記錄類型，以支援這種情況。 為了支援較舊的名稱伺服器，RFC 也允許使用 TXT 記錄類型來指定 SPF 記錄。  這種模稜兩可曾經造成混淆，但已透過 [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1) 解決。  這指出了我們應該只使用 TXT 記錄類型來建立 SPF 記錄，並取代 SPF 記錄類型。

Azure DNS 支援 SPF 記錄，因此應該使用 TXT 記錄類型來建立。 不支援過時的 SPF 記錄類型。 當[匯入 DNS 區域檔案](dns-import-export.md)時，任何使用 SPF 記錄類型的 SPF 記錄，都會轉換成 TXT 記錄類型。

### <a name="srv-records"></a>SRV 記錄

各種服務都會使用 [SRV 記錄](https://en.wikipedia.org/wiki/SRV_record)來指定伺服器位置。 在 Azure DNS 中指定 SRV 記錄時︰

* 必須在記錄集名稱中包括 [服務] 和 [通訊協定]，並在其前方加上底線。  例如，'\_sip.\_tcp.name'。  在區域頂點的記錄，則不需要在記錄名稱中包括 '@'，只要使用服務與通訊協定即可，例如 '\_sip.\_tcp'。
* 系統已將 [優先順序]、[權數]、[連接埠] 和 [目標]，指定為記錄集內每筆記錄的參數。

## <a name="tags-and-metadata"></a>標記和中繼資料

### <a name="tags"></a>標記

標記是名稱-值組的清單，由 Azure Resource Manager 用來標示資源。  Azure Resource Manager 會使用標記來啟用 Azure 帳單篩選過的檢視，也可讓您設定標記需要的原則。 如需標記的詳細資訊，請參閱 [使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

Azure DNS 支援在 DNS 區域資源上，使用 Azure Resource Manager 標記。  但不支援 DNS 記錄集上的標記。

### <a name="metadata"></a>中繼資料

Azure DNS 支援使用 '中繼資料' 來替代記錄集標記，用以標註記錄集。  類似於標記，中繼資料可讓您建立名稱-值組與每個記錄集之間的關聯。  這非常有用，例如，可用以記錄每個記錄集的目的。  與標記不同的是，中繼資料無法用來提供 Azure 帳單篩選過的檢視，也無法在 Azure Resource Manager 原則中指定。

## <a name="limits"></a>限制

使用 Azure DNS 時，會適用下列的預設限制︰

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>後續步驟

* 若要開始使用 Azure DNS，請學習如何[建立 DNS 區域](dns-getstarted-create-dnszone-portal.md)和[建立 DNS 記錄](dns-getstarted-create-recordset-portal.md)。
* 若要移轉現有的 DNS 區域，請學習如何[匯入和匯出 DNS 區域檔案](dns-import-export.md)。




<!--HONumber=Nov16_HO3-->


