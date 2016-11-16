DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始裝載您的網域，您必須建立 DNS 區域。 針對特定網域建立的任何 DNS 記錄，都位於該網域的 DNS 區域內。

例如，網域 "contoso.com" 可能包含許多的 DNS 記錄，例如 "mail.contoso.com" (用於郵件伺服器) 和 "www.contoso.com" (用於網站)。

## <a name="a-namenamesaabout-dns-zone-names"></a><a name="names"></a>關於 DNS 區域名稱
* 區域的名稱在資源群組內必須是唯一的，且區域必須尚未存在。 否則作業會失敗。
* 不同的資源群組或不同的 Azure 訂用帳戶中，可重複使用相同的區域名稱。
* 雖然多個區域共用相同的名稱，但每個執行個體會被指派不同的名稱伺服器位址，而且從父系網域只能委派一個執行個體。 如需詳細資訊，請參閱 [將網域委派給 Azure DNS](../articles/dns/dns-domain-delegation.md)。


<!--HONumber=Nov16_HO2-->


