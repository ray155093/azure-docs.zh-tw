<properties
   pageTitle="使用 CLI 建立 DNS 區域 | Microsoft Azure"
   description="了解如何使用 CLI 逐步建立 Azure DNS 的 DNS 區域來開始裝載 DNS 網域"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# 使用 CLI 建立 Azure DNS 區域


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)


本文將逐步引導您完成使用 CLI 建立 DNS 區域的步驟。您也可以使用 PowerShell 或 Azure 入口網站來建立 DNS 區域。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## 開始之前

這些指示使用 Microsoft Azure CLI。請務必更新至最新的 Azure CLI (0.9.8 或更新版本)，才能使用 Azure DNS 命令。請輸入 `azure -v`，以檢查哪一個 Azure CLI 版本目前安裝在您的電腦。

## 步驟 1 - 設定 Azure CLI

### 1\.安裝 Azure CLI

您可以安裝適用於 Windows、Linux 或 MAC 的 Azure CLI。必須先完成下列步驟，才能使用 Azure CLI 管理 Azure DNS。您可以在[安裝 Azure CLI](../xplat-cli-install.md) 中取得詳細資訊。此 DNS 命令需要 Azure CLI 0.9.8 版或更新版本。

使用下列命令，可以找到 CLI 上的所有網路提供者命令：

	Azure network

### 2\.切換 CLI 模式

Azure DNS 使用 Azure Resource Manager。請確定您已將 CLI 模式切換為使用 ARM 命令。

	Azure config mode arm

### 3\.登入您的 Azure 帳戶

系統會提示使用您的認證進行驗證。請注意，您只能使用 ORGID 的帳戶。

    Azure login -u "username"

### 4\.選取訂用帳戶

選擇要使用哪一個 Azure 訂用帳戶。

    Azure account set "subscription name"

### 5\.建立資源群組

Azure Resource Manager 需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。然而，因為所有 DNS 資源是全球性，而非區域性，資源群組位置的選擇不會對 Azure DNS 造成影響。

如果您使用現有的資源群組，則可略過此步驟。

    Azure group create -n myresourcegroup --location "West US"


### 6\.註冊

Azure DNS 服務由 Microsoft.Network 資源提供者管理。您的 Azure 訂用帳戶必須註冊為使用此資源提供者，您才能使用 Azure DNS。每個訂用帳戶只需執行一次此作業。

	Azure provider register --namespace Microsoft.Network


## 步驟 2 - 建立 DNS 區域

使用 `azure network dns zone create` 命令建立 DNS 區域。您可以選擇建立 DNS 區域以及標記。標記是名稱-值組的清單，由 Azure Resource Manager 在計費或分群用途上用來標示資源。如需標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

在 Azure DNS 中，指定的區域名稱結尾不能有 **‘.’**。例如，指定為 '**contoso.com**'，而非 '**contoso.com**'。


### 建立 DNS 區域

下列範例會在稱為「MyResourceGroup」的資源群組中建立稱為「contoso.com」的 DNS 區域。

使用範例來建立您的 DNS 區域，並將值替換為您自己的值。

    Azure network dns zone create myresourcegroup contoso.com

### 建立 DNS 區域和標記

Azure DNS CLI 透過使用選擇性的「-Tag」參數來支援所指定 DNS 區域的標記。下列範例示範如何使用兩個標記 project = demo 和 env = test 建立 DNS 區域。

使用下列範例來建立 DNS 區域和標記，並將值替換為您自己的值。

	Azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## 檢視記錄

建立 DNS 區域也會建立下列 DNS 記錄：

- 「起始點授權」(SOA) 記錄。這出現在每個 DNS 區域的根。

- 授權名稱伺服器 (NS) 記錄。這些顯示哪些名稱伺服器裝載該區域。Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可以指派至 Azure DNS 中的不同區域。如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

若要檢視這些記錄，請使用 `azure network dns-record-set show`。<BR> 使用方式：network dns record-set show <resource-group> <dns-zone-name> <name> <type>


在下列範例中，如果您使用資源群組 myresourcegroup、記錄集名稱 "@" (適用於根記錄) 和類型 SOA 執行命令，則會產生下列輸出：


	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> 若要檢視使用區域建立的 NS 記錄，請使用下列命令：

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE] 位於 DNS 區域的根 (或「頂點」) 的記錄集使用 **@** 做為記錄集名稱。

## 測試

您可以使用 nslookup、DIG 或 `Resolve-DnsName` PowerShell Cmdlet 等 DNS 工具測試您的 DNS 區域。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。如上面的 "azure network dns record-set show" 所列，NS 記錄提供您區域的名稱伺服器。請務必在下列命令中用正確的值取代您的區域。

下列範例使用 DIG，使用為 DNS 區域指派的名稱伺服器來查詢網域 contoso.com。使用 DIG 的查詢必須指向我們將 @<區域的名稱伺服器> 和區域名稱用於的名稱伺服器。

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## 後續步驟

建立 DNS 區域之後，請建立[記錄集和記錄](dns-getstarted-create-recordset-cli.md)，以開始解析您的網際網路網域名稱。

<!---HONumber=AcomDC_0817_2016-->