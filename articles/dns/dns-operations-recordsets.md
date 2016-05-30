<properties 
   pageTitle="在 Azure DNS 管理 DNS 記錄集和記錄 |Microsoft Azure" 
   description="將網域裝載於 Azure DNS 時，在 Azure DNS 管理 DNS 記錄集和記錄。對記錄集和記錄執行作業的所有 PowerShell 命令。" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# 使用 PowerShell 管理 DNS 記錄集和記錄集



> [AZURE.SELECTOR]
- [Azure 入口網站](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



本文說明如何使用 Azure PowerShell 來管理 DNS 區域的記錄集和記錄。

請務必了解 DNS 記錄集和個別 DNS 記錄之間的差別。記錄集是指一個區域中有相同名稱和相同類型的記錄集合。如需詳細資訊，請參閱[了解記錄集和記錄](dns-getstarted-create-recordset-portal.md)。

為了管理記錄集和記錄，您必須安裝最新版的 Azure Resource Manager PowerShell Cmdlet。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。如需使用 PowerShell 的詳細資訊，請參閱[搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。



## 建立新的記錄集和記錄

若要在 Azure 入口網站中建立記錄集，請參閱[使用 Azure 入口網站建立 DNS 記錄](dns-getstarted-create-recordset.md)。

## 取得記錄集

若要擷取現有的記錄集，使用 `Get-AzureRmDnsRecordSet`。指定記錄集相對名稱、記錄類型、區域。

	$rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

如同 `New-AzureRmDnsRecordSet`，記錄名稱必須是相對名稱，也就是不含區域名稱。

區域可以使用區域名稱和資源群組名稱或區域物件來指定：

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone
	
`Get-AzureRmDnsRecordSet` 傳回本機物件，代表 Azure DNS 中建立的記錄集。

## 列出記錄集

`Get-AzureRmDnsRecordSet` 也可用來列出記錄集，只要略過 *–Name* 和/或 *–RecordType* 參數。

### 列出所有記錄集 

此範例會傳回所有記錄集，不論其名稱或記錄類型：

	$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### 列出指定記錄類型的記錄集

此範例會傳回符合指定記錄類型的所有記錄集。在此案例中是 A 記錄。

	$list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 

可以使用 *–ZoneName* 和 *–ResourceGroupName* 參數來指定區域 (如上所示)，或透過指定區域物件來指定：

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$list = Get-AzureRmDnsRecordSet -Zone $zone

## 將記錄加入至記錄集

系統會使用 `Add-AzureRmDnsRecordConfig` Cmdlet 將記錄加入記錄集。這是離線作業 — 只有代表本機物件的記錄集會變更。

將記錄加入至記錄集的參數，根據記錄集的類型而所有不同。例如，使用 *A* 類型的記錄集時，只能使用 *-IPv4Address* 參數來指定記錄。

您可以額外呼叫 `Add-AzureRmDnsRecordConfig`，將更多記錄加入每個記錄集。任何記錄集最多只能加入 20 筆記錄。不過，*CNAME* 類型的記錄集最多只能包含 1 筆記錄，一個記錄集不能包含兩筆相同的記錄。您可以建立空的記錄集 (沒有記錄)，但不會出現在 Azure DNS 名稱伺服器上。

記錄集包含所要的記錄集合之後，需要使用 `Set-AzureRmDnsRecordSet` Cmdlet 來認可它。記錄集一旦被認可，就會以提供的記錄集取代 Azure DNS 中現有的記錄集。

### 建立含有單一記錄的 A 記錄集

	$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

用來建立記錄的作業序列也可以「經由管道輸送」，亦即使用管道傳遞記錄集物件，而不是當做參數傳遞。例如：

	New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### 其他記錄類型範例

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## 修改現有記錄集

修改現有記錄集的做法與建立記錄類似。作業序列如下：

1.	使用 `Get-AzureRmDnsRecordSet` 擷取現有的記錄集。

2.	修改記錄集，包括加入記錄、移除記錄、變更記錄參數或變更記錄集 TTL。這些變更是離線進行 — 只有代表記錄集的本機物件會變更。

3.	使用 `Set-AzureRmDnsRecordSet` Cmdlet 來認可您所做的變更。這會以提供的記錄集取代 Azure DNS 中現有的記錄集。


### 更新現有記錄集中的記錄

在此範例中，我們變更現有 A 記錄的 IP 位址：

	$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Ipv4Address = "134.170.185.46"
	Set-AzureRmDnsRecordSet -RecordSet $rs 

`Set-AzureRmDnsRecordSet` 使用 *Etag* 檢查來確保不會覆寫並行變更。使用 *-Overwrite* 旗標來停用這些檢查。如需詳細資訊，請參閱[關於Etag 和標記](dns-getstarted-creatednszone.md#tagetag)。

### 修改 SOA 記錄

您無法從區域頂點 (名稱 = '@') 中自動建立的 SOA 記錄集加入或移除記錄，但是您可以修改 SOA 記錄 ('Host' 除外) 和記錄集 TTL 內的任何參數。

下列範例示範如何變更 SOA 記錄的 *Email* 屬性：

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Email = "admin.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs 

### 在區域頂點修改 NS 記錄

您無法在區域頂點 (名稱 = '@') 自動建立的 NS 記錄集加入、移除或修改記錄。修改記錄集 TTL 是唯一允許的變更。

下列範例示範如何變更 NS 記錄集的 TTL 屬性：

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Ttl = 300
	Set-AzureRmDnsRecordSet -RecordSet $rs 

### 將記錄加入至現有的記錄集

在此範例中，我們將兩個額外的 MX 記錄加入至現有的記錄集：

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	Set-AzureRmDnsRecordSet -RecordSet $rs 

## 從現有的記錄集移除記錄

您可以使用 `Remove-AzureRmDnsRecordConfig` 移除記錄集中的記錄。請注意，要移除的記錄必須完全符合現有的資料錄，包括所有參數。必須使用 `Set-AzureRmDnsRecordSet` 認可變更。

移除記錄集的最後一筆記錄不會刪除記錄集。如需詳細資訊，請參閱下方的[刪除記錄集](#delete-a-record-set)。


	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

用來從記錄集移除記錄的作業序列也可以「經由管道輸送」，亦即使用管道傳遞記錄集物件，而不是當做參數傳遞。例如：

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### 從記錄集移除 AAAA 記錄

	$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 從記錄集移除 CNAME 記錄

因為 CNAME 記錄集最多只能包含一筆記錄，移除該記錄會留下空的記錄集。

	$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 從記錄集移除 MX 記錄

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 從記錄集移除 NS 記錄
	
	$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 從記錄集移除 SRV 記錄

	$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### 從記錄集移除 TXT 記錄

	$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs

## 刪除記錄集

可以使用 `Remove-AzureRmDnsRecordSet` Cmdlet 刪除記錄集。您無法在建立區域時所自動建立的區域頂點 (名稱 = ‘@’) 刪除 SOA 和 NS 記錄集。刪除區域時會自動加以刪除。

使用下列三種方式之一來移除記錄集：

### 依名稱指定所有參數

選擇性的 *-Force* 參數可用來隱藏確認提示。

	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### 依名稱和類型指定記錄集，依物件指定區域

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### 依物件指定記錄集：

	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

使用物件指定記錄集可啟用 Etag 檢查，來確保不會刪除並行的變更。選擇性的 *-Overwrite* 旗標可停用這些檢查。如需詳細資訊，請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#tagetag)。

記錄集物件也可以經由管道輸送，而不是當做參數傳遞：

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## 後續步驟

如需 Azure DNS 的詳細資訊，請參閱 [Azure DNS 概觀](dns-overview.md)。如需自動化 DNS 的相關資訊，請參閱[使用 .NET SDK 建立 DNS 區域和記錄集](dns-sdk.md)。

如果您想要使用反向 DNS 記錄，請參閱[如何管理反向 DNS 記錄](dns-reverse-dns-record-operations-ps.md)。
 

<!---HONumber=AcomDC_0518_2016-->