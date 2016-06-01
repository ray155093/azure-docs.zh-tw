<properties 
   pageTitle="使用 Azure CLI 在 Azure DNS 管理 DNS 記錄集和記錄 | Microsoft Azure" 
   description="將網域裝載於 Azure DNS 時，在 Azure DNS 管理 DNS 記錄集和記錄。對記錄集和記錄執行作業的所有 CLI 命令。" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# 使用 CLI 管理 DNS 記錄集和記錄集


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


本文章說明如何使用跨平台 Azure CLI 管理 DNS 區域的記錄集和記錄。

請務必了解 DNS 記錄集和個別 DNS 記錄之間的差別。記錄集是指一個區域中有相同名稱和相同類型的記錄集合。如需詳細資訊，請參閱[了解記錄集和記錄](dns-getstarted-create-recordset-cli.md)。


## Azure DNS 和跨平台 Azure CLI

Azure DNS 是僅能以 Azure 資源管理員運作的服務。它沒有 ASM API。您需要確定 Azure CLI 已設定為使用 Resource Manager 模式。您可以使用 `azure config mode arm` 命令執行此工作。<BR>如果您看到「錯誤：'dns' 不是 azure 命令」訊息，可能是因為您正在 ASM 模式中使用 Azure CLI，而非 Resource Manager 模式中。

## 建立新的記錄集和記錄

若要在 Azure 入口網站中建立記錄集，請參閱[建立記錄集和記錄](dns-getstarted-create-recordset-cli.md)。


## 擷取記錄集

若要擷取現有的記錄集，使用 `azure network dns record-set show`。指定資源群組、區域名稱、記錄集相對名稱、記錄類型。使用下列範例，將其中的值替換為您自己的值。

	azure network dns record-set show myresourcegroup contoso.com www A


## 列出記錄集

您可以使用 `azure network dns record-set list` 命令列出 DNS 區域中的所有記錄。您需要指定資源群組名稱和區域名稱。

### 列出所有記錄集

此範例會傳回所有記錄集，不論其名稱或記錄類型：

	azure network dns record-set list myresourcegroup contoso.com

### 列出指定類型的記錄集

此範例會傳回符合指定記錄類型 (此案例中為 A 記錄) 的所有記錄集。

	azure network dns record-set list myresourcegroup contoso.com A 


## 將記錄加入至記錄集

系統會使用 `azure network dns record-set add-record` 將記錄加入記錄集。將記錄加入至記錄集的參數，根據記錄集的類型而所有不同。例如，使用 *A* 類型的記錄集時，您只能使用參數 `-a <IPv4 address>` 來指定記錄。

若要建立記錄集，請使用 `azure network dns record-set create`。指定資源群組、區域名稱、記錄集相對名稱、記錄類型、存留時間 (TTL)。若未定義 --ttl 參數，預設值為 4 (以秒為單位)。
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


建立 A 記錄集之後，使用 `azure network dns record-set add-record` 加入 IPv4 位址。

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 


下列範例示範如何建立每一種記錄類型的記錄集 (包含單一記錄)。

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## 更新記錄集中的記錄

### 將另一個 IP 位址 (1.2.3.4) 加入現有的 A 記錄集 (www)： 

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK

### 使用 `azure network dns record-set delete-record` 移除記錄集中現有的值。
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:    IPv4 address                    : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## 從記錄集移除記錄

您可以使用 `azure network dns record-set delete-record` 移除記錄集中的記錄。要移除的記錄必須完全符合現有的資料錄，包括所有參數。

移除記錄集的最後一筆記錄不會刪除記錄集。如需詳細資訊，請參閱本文結尾的[刪除記錄集](#delete)一節。

	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### 從記錄集移除 AAAA 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### 從記錄集移除 CNAME 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### 從記錄集移除 MX 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### 從記錄集移除 NS 記錄
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### 從記錄集移除 SRV 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### 從記錄集移除 TXT 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>刪除記錄集

可以使用 `Remove-AzureRmDnsRecordSet` Cmdlet 刪除記錄集。您無法在建立區域時所自動建立的區域頂點 (名稱 = ‘@’) 刪除 SOA 和 NS 記錄集。刪除區域時會自動加以刪除。

在下列範例中，A 記錄集 "test-a" 將會從 contoso.com DNS 區域移除：

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

選擇性的 *-q* 參數可用來隱藏確認提示。


## 後續步驟

如需 Azure DNS 的詳細資訊，請參閱 [Azure DNS 概觀](dns-overview.md)。如需自動化 DNS 的相關資訊，請參閱[使用 .NET SDK 建立 DNS 區域和記錄集](dns-sdk.md)。

如果您想要使用反向 DNS 記錄，請參閱[如何管理反向 DNS 記錄](dns-reverse-dns-record-operations-cli.md)。




 

<!---HONumber=AcomDC_0518_2016-->