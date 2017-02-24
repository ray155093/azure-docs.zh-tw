
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>常見問題集 - Azure 指派的 IP 位址的反向 DNS

### <a name="how-much-do-reverse-dns-records-cost"></a>反向 DNS 記錄的成本為何？

完全免費！  反向 DNS 記錄或查詢不需要額外成本。

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>我的 Azure 指派的公用 IP 位址的反向 DNS 記錄將可從網際網路解析？

 是。 在您為公用 IP 位址設定反向 DNS 屬性之後，Azure 會管理所有必要的 DNS 委派和 DNS 區域，以確保反向 DNS 記錄可以為所有網際網路使用者解析。

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>將會針對我的公用 IP 位址建立預設的反向 DNS 記錄嗎？

不會。 反向 DNS 是選用的功能。 如果您選擇不設定，則不會建立任何預設反向 DNS 記錄。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完整網域名稱 (FQDN) 的格式為何？

FQDN 是以正向順序指定，且必須以點結束 (例如，"app1.contoso.com.")。

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>如果我指定的反向 DNS 驗證檢查失敗，會發生什麼事？

如果反向 DNS 的驗證檢查失敗，則服務管理作業也會失敗。 請依要求更正反向 DNS 值，然後再試一次。

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>我可以管理我的 Azure 網站的反向 DNS 嗎？

Azure 網站不支援反向 DNS。 Azure 虛擬機器支援反向 DNS。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>我可以針對公用 IP 位址設定多個反向 DNS 記錄嗎？

不會。 Azure 針對每個公用 IP 位址支援單一反向 DNS 記錄。 不過，每個公用 IP 位址可以有自己的反向 DNS 記錄。

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>我可以針對 IPv6 公用 IP 位址設定反向 DNS 記錄嗎？

否。  目前，反向 DNS 記錄僅支援 IPv4 公用 IP 位址。

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>我可以針對公用 IP 位址設定反向 DNS 記錄，而不需要指定 DomainNameLabel 嗎？

不會。 若要針對公用 IP 位址運用反向 DNS 記錄，您必須指定 DomainNameLabel 屬性。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>我可以從我的 Azure 計算服務將電子郵件傳送至外部網域嗎？

否。 [Azure 計算服務不支援將電子郵件傳送至外部網域](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。


<!--HONumber=Nov16_HO3-->


