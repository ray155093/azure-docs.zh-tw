## <a name="about-records"></a>關於記錄

每一筆 DNS 記錄都有名稱和類型。 記錄根據所包含的資料而組織成各種類型。 最常見的類型為 "A" 記錄，可將名稱對應到 IPv4 位址。 另一個類型為 "MX" 記錄，可將名稱對應到郵件伺服器。

Azure DNS 支援所有常見的 DNS 記錄類型，包括 A、AAAA、CNAME、MX、NS、PTR、SOA、SRV 和 TXT。 請注意：
- 每個區域會自動建立 SOA 記錄集，無法另外建立。
- 應該使用 TXT 記錄類型建立 SPF 記錄。 如需詳細資訊，請參閱 [此頁面](http://tools.ietf.org/html/rfc7208#section-3.1)。

在 Azure DNS 中，記錄是使用相對名稱來指定。 「完整」網域名稱 (FQDN) 包含區域名稱，而「相對」名稱不含區域名稱。 比方說，區域 "contoso.com" 中的相對記錄名稱 "www" 就給出完整記錄名稱 www.contoso.com。

## <a name="about-record-sets"></a>關於記錄集

有時候，您需要以指定的名稱和類型建立多筆 DNS 記錄。 例如，假設 "www.contoso.com" 網站裝載於兩個不同的 IP 位址。 網站需要兩筆不同的 A 記錄，每個 IP 位址各一筆。 這是記錄集的範例。

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS 使用記錄集來管理 DNS 記錄。 記錄集是指一個區域中有相同名稱和相同類型的 DNS 記錄集合。 大部分的記錄集只包含單一記錄，但像是記錄集包含多筆記錄的例子也屢見不鮮。

SOA 和 CNAME 記錄集是例外狀況。 DNS 標準不允許這些類型有多筆相同名稱的記錄。

存留時間，或 TTL，指定每一筆記錄由用戶端快取多久，之後才會重新查詢。 在此範例中，TTL 是 3600 秒 (1 小時)。 TTL 是針對記錄集而指定，而非針對每一筆記錄，因此相同的值套用至該記錄集內的所有記錄。

#### <a name="wildcard-record-sets"></a>萬用字元記錄集

Azure DNS 支援 [萬用字元記錄](https://en.wikipedia.org/wiki/Wildcard_DNS_record)。 會針對具有相符名稱的任何查詢傳回 (除非有來自非萬用字元記錄集的更接近相符項目)。 針對所有記錄類型支援 NS 和 SOA 以外的所有萬用字元記錄集。  

若要建立萬用字元記錄集，請使用記錄集名稱 "\*"。 或者，使用具有標籤 "\*" 的名稱，例如 "\*.foo"。

#### <a name="cname-record-sets"></a>CNAME 記錄集

CNAME 記錄集不能與其他具有相同名稱的記錄集共存。 例如，您無法同時建立具有相對名稱 "www" 的 CNAME 記錄集和具有相對名稱 "www" 的 A 記錄。 因為區域頂點 (名稱 = ‘@’)) 一定會包含建立區域時所建立的 NS 和 SOA 記錄集，您無法在區域頂點建立 CNAME 記錄集。 這些條件約束源自於 DNS 標準，並不是 Azure DNS 的限制。


<!--HONumber=Oct16_HO2-->


