<properties
   pageTitle="使用 PowerShell 建立 DNS 區域的記錄集和記錄 | Microsoft Azure"
   description="如何建立 Azure DNS 的主機記錄。使用 PowerShell 設定記錄集和記錄"
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
   ms.date="08/16/2016"
   ms.author="cherylmc"/>



# 使用 PowerShell 建立 DNS 記錄集和記錄


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)

本文將逐步引導您完成使用 Windows PowerShell 建立記錄和記錄集的程序。建立 DNS 區域之後，您將會新增網域的 DNS 記錄。若要這樣做，您必須先了解 DNS 記錄和記錄集。

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## 確認您擁有最新版本的 PowerShell

請確認您已安裝最新版的 Azure Resource Manager PowerShell Cmdlet。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## 建立記錄集和記錄

本節將說明如何建立記錄集與記錄。


### 1\.連線至您的訂用帳戶

開啟 PowerShell 主控台並連接到您的帳戶。使用下列範例來協助您連接：

	Login-AzureRmAccount

檢查帳戶的訂用帳戶。

	Get-AzureRmSubscription

指定您要使用的訂用帳戶。

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

如需使用 PowerShell 的詳細資訊，請參閱[搭配使用 Windows PowerShell 與 Resource Manager](../powershell-azure-resource-manager.md)。


### 2\.建立記錄集

您可以使用 `New-AzureRmDnsRecordSet` Cmdlet 來建立記錄集。建立記錄集時，您必須指定記錄集名稱、區域、存留時間 (TTL) 和記錄類型。

若要在區域頂點 (在此案例中為 'contoso.com') 建立記錄集，請使用記錄名稱 "@" (包括引號)。這是常見的 DNS 慣例。

下列範例會在 DNS 區域 "contoso.com" 中建立具有相對名稱 "www" 的記錄集。記錄集的完整名稱是 "www.contoso.com"。記錄類型為 "A"，且 TTL 為 60 秒。完成此步驟之後，您就具有指派給變數 *$rs* 的空 "www" 記錄集。

	$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### 如果記錄集已經存在

如果記錄集已經存在，除非使用 *-Overwrite* 參數，否則命令會失敗。*-Overwrite* 選項會觸發確認提示，但可使用 *-Force* 參數來隱藏提示。


	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


在此範例中，您可以使用區域名稱和資源群組名稱來指定區域。或者，您可以指定區域物件，如同 `Get-AzureRmDnsZone` 或 `New-AzureRmDnsZone` 所傳回。

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet` 會傳回本機物件，代表在 Azure DNS 中建立的記錄集。

### 3\.新增記錄

若要使用新建立的 "www" 記錄集，您必須對其新增記錄。您可以使用下列範例，將 IPv4 *A* 記錄新增至 "www" 記錄集。這個範例會依賴您在上一個步驟中設定的變數 *$rs*。

使用 `Add-AzureRmDnsRecordConfig` 將記錄新增至記錄集是離線作業。只有本機變數 *$rs* 會更新。


	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### 4\.認可變更

認可對記錄集所做的變更。使用 `Set-AzureRmDnsRecordSet` 將記錄集的變更上傳到 Azure DNS。

	Set-AzureRmDnsRecordSet -RecordSet $rs

### 5\.擷取記錄集

您可以使用 `Get-AzureRmDnsRecordSet` 從 Azure DNS 擷取記錄集，如下列範例所示。


	Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}


您也可以使用 nslookup 工具或其他 DNS 工具來查詢新的記錄集。

如果您還沒有將網域委派給 Azure DNS 名稱伺服器，就必須明確指定區域的名稱、伺服器及位址。


	nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221

## 針對每一種類型建立含有單一記錄的記錄集


下列範例示範如何建立每一種記錄類型的記錄集。每個記錄集都會包含一筆記錄。

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## 後續步驟

[如何管理使用 PowerShell 的 DNS 區域](dns-operations-dnszones.md)

[使用 PowerShell 管理 DNS 記錄集和記錄集](dns-operations-recordsets.md)

[使用 .NET SDK 自動化 Azure 作業](dns-sdk.md)

<!---HONumber=AcomDC_0817_2016-->