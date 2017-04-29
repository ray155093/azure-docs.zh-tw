## <a name="what-is-reverse-dns"></a>什麼是反向 DNS？

傳統 DNS 記錄可啟用從 DNS 名稱至 (例如 'www.contoso.com') IP 位址 (例如 64.4.6.100) 的對應。  反向 DNS 則可將 IP 位址 (64.4.6.100) 轉譯回名稱 ('www.contoso.com')。

反向 DNS 記錄使用於多種情況。 例如，透過驗證電子郵件訊息的寄件者，反向 DNS 記錄廣泛用於對抗垃圾電子郵件。  接收端郵件伺服器會擷取傳送端伺服器 IP 位址的反向 DNS 記錄，並確認該主機是否獲授權從原始網域傳送電子郵件。 (不過請注意，[Azure 計算服務不支援將電子郵件傳送至外部網域](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。)

## <a name="how-reverse-dns-works"></a>反向 DNS 的運作方式

反向 DNS 記錄託管於特殊的 DNS 區域，稱為 'ARPA' 區域。  這些區域會與一般階層託管網域 (例如 'contoso.com') 同時形成個別的 DNS 階層。

例如，DNS 記錄 'www.contoso.com' 是使用 DNS 'A' 記錄名稱 'www' 在區域 'contoso.com' 中實作的。  此 A 記錄會指向對應的 IP 位址，在此情況下為 64.4.6.100。  反向對應會在區域 '6.4.64.in-addr.arpa' 中使用名為 '100' 的 'PTR' 記錄分別實作 (請注意，IP 位址會在 ARPA 區域中反轉)。此 PTR 記錄 (如果它已正確設定) 會指向名稱 'www.contoso.com'。

當組織獲指派 IP 位址區塊時，也會取得管理對應的 ARPA 區域的權限。 對應至 Azure 所使用 IP 位址區塊的 ARPA 區域會由 Microsoft 託管並管理。 您的 ISP 可能會為您託管您自己的 IP 位址的 ARPA 區域，或可能讓您在所選的 DNS 服務 (例如，Azure DNS) 中託管 ARPA 區域。

> [!NOTE]
> 正向 DNS 對應與反向 DNS 對應會在個別的平行 DNS 階層中實作。 'www.contoso.com' 的反向對應**不是**在區域 'contoso.com' 中託管，而是在對應的 IP 位址區塊的 ARPA 區域中託管。

如需有關反向 DNS 的詳細資訊，請參閱[反向 DNS 對應](http://en.wikipedia.org/wiki/Reverse_DNS_lookup)。

## <a name="azure-support-for-reverse-dns"></a>Azure 的反向 DNS 支援

Azure 支援與反向 DNS 相關的兩種不同案例：

1. 託管對應至您的 IP 位址區塊的 ARPA 區域。
2. 可讓您設定指派給 Azure 服務之 IP 位址的反向 DNS 記錄。

若要支援前者，Azure DNS 可以用來託管 ARPA 區域和管理每個反向 DNS 對應的 PTR 記錄。  建立 ARPA 區域、設定委派，以及設定 PTR 記錄的程序與一般的 DNS 區域相同。  唯一的差異是必須透過您的 ISP 設定委派，而不是 DNS 註冊機構，而且僅應該使用 PTR 記錄類型。

若要支援後者，Azure 可讓您設定配置給您的服務之 IP 位址的反向對應。  此反向對應是由 Azure 設定作為對應 ARPA 區域中的 PTR 記錄。  對應至 Azure 所使用 IP 位址範圍的這些 ARPA 區域會由 Microsoft 託管。 **這篇文章的其餘部分將詳細說明這個案例。**
