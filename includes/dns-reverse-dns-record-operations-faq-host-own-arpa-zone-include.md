
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>常見問題集 - 在 Azure DNS 中託管您的 ARPA 區域

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>我可以在 Azure DNS 上，為 ISP 指派的 IP 區塊裝載 ARPA 區域嗎？

 是。 完全支援在 Azure DNS 中託管您自己的 IP 範圍的 ARPA 區域。

只要[在 Azure DNS 中建立區域](../articles/dns/dns-getstarted-create-dnszone.md)，然後與您的 ISP 合作來[委派區域](../articles/dns/dns-domain-delegation.md)。  接著，您就可以使用與其他記錄類型相同的方式來管理每個反向對應的 PTR 記錄。

您也可以[使用 Azure CLI 匯入現有的反向對應區域](../articles/dns/dns-import-export.md)。

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>託管我的 ARPA 區域需要多少費用？

在 Azure DNS 中託管 ISP 所指派 IP 區塊的 ARPA 區域，會以[標準 Azure DNS 費率](https://azure.microsoft.com/pricing/details/dns/)計費。

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>我可以在 Azure DNS 中同時託管 IPv4 和 IPv6 位址的 ARPA 區域？

 是。


<!--HONumber=Nov16_HO3-->


