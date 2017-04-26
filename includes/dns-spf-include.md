寄件者原則架構 (SPF) 記錄，是用來指定可以代表指定的網域名稱，傳送電子郵件的電子郵件伺服器。  請務必正確設定 SPF 記錄，以防止收件者將您的電子郵件標示為垃圾郵件。

DNS RFC 原本推出了新 'SPF' 記錄類型，以支援這種情況。 為了支援較舊的名稱伺服器，RFC 也允許使用 TXT 記錄類型來指定 SPF 記錄。  這種模稜兩可曾經造成混淆，但已透過 [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1) 解決。  其中出應該只使用 TXT 記錄類型來建立 SPF 記錄，且 SPF 記錄類型已被取代。

Azure DNS 支援 SPF 記錄，因此應該使用 TXT 記錄類型來建立。 不支援過時的 SPF 記錄類型。 當[匯入 DNS 區域檔案](../articles/dns/dns-import-export.md)時，任何使用 SPF 記錄類型的 SPF 記錄，都會轉換成 TXT 記錄類型。