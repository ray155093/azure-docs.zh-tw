#### <a name="create-an-a-record-set-with-single-record"></a>建立含有單一記錄的 A 記錄集
若要建立記錄集，請使用 `azure network dns record-set create`。 指定資源群組、區域名稱、記錄集相對名稱、記錄類型以及存留時間 (TTL)。

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

建立 A 記錄集之後，使用 `azure network dns record-set add-record`將 IPv4 位址加入記錄集。

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>建立含有單一記錄的 AAAA 記錄集
    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>建立含有單一記錄的 CNAME 記錄集
CNAME 記錄只允許一個單一字串值。

    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>建立含有單一記錄的 MX 記錄集
在此範例中，會使用記錄集名稱 "@"，在區域頂點 (在此案例中，"contoso.com") 建立 MX 記錄。 對於 MX 記錄而言，這很常見。

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>建立含有單一記錄的 NS 記錄集
    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>建立含有單一記錄的 PTR 記錄集
在此情況下，'my-arpa-zone.com' 代表表示您 IP 範圍的 ARPA 區域。  此區域中的每個 PTR 記錄集都與此 IP 範圍內的一個 IP 位址相對應。    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>建立含有單一記錄的 SRV 記錄集
如果您要在區域的根內建立 SRV 記錄，您可以在記錄名稱中指定 "_service" 和 "_protocol"。 記錄名稱中不需要包含 "@"。

    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>建立含有單一記錄的 TXT 記錄集
    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"


<!--HONumber=Oct16_HO2-->


