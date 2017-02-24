
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>常見問題集 - Azure 指派的 IP 位址的反向 DNS

### <a name="how-much-do-reverse-dns-records-cost"></a>反向 DNS 記錄的成本為何？

完全免費！  反向 DNS 記錄或查詢不需要額外成本。

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>我的反向 DNS 記錄會從網際網路解析嗎？

是。 在您為「雲端服務」設定反向 DNS 屬性之後，Azure 會管理所有必要的 DNS 委派和 DNS 區域，以確保反向 DNS 記錄可以為所有網際網路使用者解析。

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>我的雲端服務會建立預設反向 DNS 記錄嗎？

不會。 反向 DNS 是選用的功能。 如果您選擇不設定，則不會建立任何預設反向 DNS 記錄。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完整網域名稱 (FQDN) 的格式為何？

FQDN 是以正向順序指定，且必須以點結束 (例如，"app1.contoso.com.")。

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>如果我指定的反向 DNS 驗證檢查失敗，會發生什麼事？

如果反向 DNS 的驗證檢查失敗，則服務管理作業也會失敗。 請依要求更正反向 DNS 值，然後再試一次。

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>我可以管理我的 Azure 網站的反向 DNS 嗎？

Azure 網站不支援反向 DNS。 Azure PaaS 角色與 IaaS 虛擬機器則支援反向 DNS。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>我可以針對我的「雲端服務」設定多個反向 DNS 記錄嗎？

不會。 Azure 針對每個 Azure 雲端服務支援單一反向 DNS 記錄。 不過，每個 Azure 雲端服務可以有自己的反向 DNS 記錄。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>我可以從我的 Azure 計算服務將電子郵件傳送至外部網域嗎？

否。 [Azure 計算服務不支援將電子郵件傳送至外部網域](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。


<!--HONumber=Nov16_HO3-->


