<properties
   pageTitle="使用 Azure 入口網站建立 DNS 區域的記錄集和記錄 | Microsoft Azure"
   description="如何使用 Azure 入口網站建立 Azure DNS 的主機記錄並建立記錄集和記錄"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="cherylmc"/>


# 使用 Azure 入口網站建立 DNS 記錄和記錄集


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


建立 DNS 區域之後，您必須加入網域的 DNS 記錄。若要這樣做，您必須先了解 DNS 記錄和記錄集。

## 了解記錄集和記錄

### 關於記錄

每一筆 DNS 記錄都有名稱和類型。

「完整」網域名稱 (FQDN) 包含區域名稱，而「相對」名稱不含區域名稱。比方說，區域 "contoso.com" 中的相對記錄名稱 "www" 就給出完整記錄名稱 "www.contoso.com"。

>[AZURE.NOTE] 在 Azure DNS 中，記錄是使用相對名稱來指定。

記錄根據所包含的資料而有各種類型。最常見的類型為 "**A**" 記錄，可將名稱對應到 IPv4 位址。另一個類型為 "**MX**" 記錄，可將名稱對應到郵件伺服器。

Azure DNS 支援所有常見的 DNS 記錄類型：A、AAAA、CNAME、MX、NS、SOA、SRV 和 TXT。請注意，應該使用 TXT 記錄類型建立 SPF 記錄。如需詳細資訊，請參閱[此頁面](http://tools.ietf.org/html/rfc7208#section-3.1)。


### 關於記錄集

有時候，您需要以指定的名稱和類型建立多筆 DNS 記錄。例如，假設 www.contoso.com 網站裝載於兩個不同的 IP 位址。這需要兩筆不同的 A 記錄，每個 IP 位址各一筆。這是記錄集的範例。

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Azure DNS 使用記錄集來管理 DNS 記錄。記錄集是指一個區域中有相同名稱和相同類型的 DNS 記錄集合。大部分的記錄集只包含單一記錄，但像以上的記錄集包含多筆記錄的例子也屢見不鮮。

SOA 和 CNAME 類型的記錄集是例外；DNS 標準不允許這些類型有多筆相同名稱的記錄。

存留時間 (TTL) 指定每一筆記錄由用戶端快取多久，之後才會重新查詢。在上述範例中，TTL 是 3600 秒 (1 小時)。TTL 是針對記錄集而指定，而非針對每一筆記錄，因此相同的值套用至該記錄集內的所有記錄。

#### 萬用字元記錄集

Azure DNS 支援[萬用字元記錄](https://en.wikipedia.org/wiki/Wildcard_DNS_record)。會針對具有相符名稱的任何查詢傳回 (除非有來自非萬用字元記錄集的更接近相符項目)。針對所有記錄類型支援 NS 和 SOA 以外的所有萬用字元記錄集。

若要建立萬用字元記錄集，請使用記錄集名稱 "*"，或其第一個標籤為 "*" 的名稱，例如 "*.foo"。

#### CNAME 記錄集

CNAME 記錄集不能與其他具有相同名稱的記錄集共存。例如，您無法同時建立具有相對名稱 "www" 的 CNAME 和具有相對名稱 "www" 的 A 記錄。因為區域頂點 (名稱 = '@') 一定會包含建立區域時所建立的 NS 和 SOA 記錄集，這表示您無法在區域頂點建立 CNAME 記錄集。這些條件約束源自於 DNS 標準；並不是 Azure DNS 的限制。


## 建立新的記錄集和記錄

下列範例會逐步解說如何使用 Azure 入口網站建立記錄集和記錄。我們將使用 DNS 'A' 記錄類型。

1. 登入 Azure 管理入口網站。

2. 瀏覽至要在其中建立記錄集的 [DNS 區域] 刀鋒視窗。

3. 在您的 [DNS 區域] 刀鋒視窗頂端按一下 [記錄集] 以開啟 [新增記錄集] 刀鋒視窗。
 
	![新的記錄集](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. 在 [新增記錄集] 刀鋒視窗中，為您的記錄集命名。例如，您可以將記錄集命名為 "**www**"。
  
	![新增記錄集](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 選取您想要建立的記錄類型。例如，**A**。

6. 設定 [TTL]。入口網站中的預設值是 1 小時。

7. 新增 IP 位址，每行一個 IP 位址。使用上述的建議記錄集名稱和記錄類型，這會新增 IPv4 IP 位址到 www 記錄集的 A 記錄。

8. 當您新增好 IP 位址，按一下刀鋒視窗底部的 [確定]。隨即會建立 DNS 記錄集。

## 使用 DNS 工具測試您的 DNS 區域


如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。您的區域的名稱伺服器會顯示在您的 [DNS 區域] 刀鋒視窗的 [基本資訊] 窗格中。如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md) 一文。

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具測試您的 DNS 區域。


## 後續步驟

如需 Azure DNS 的詳細資訊，請參閱 [Azure DNS 概觀](dns-overview.md)。如需自動化 DNS 的相關資訊，請參閱[使用 .NET SDK 建立 DNS 區域和記錄集](dns-sdk.md)。

<!---HONumber=AcomDC_0406_2016-->