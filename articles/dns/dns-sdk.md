<properties 
   pageTitle="使用 .NET SDK 建立 Azure DNS 的 DNS 區域和記錄集 | Microsoft Azure" 
   description="如何使用 .NET SDK 建立 Azure DNS 的 DNS 區域和記錄集。" 
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


# 使用 .NET SDK 建立 DNS 區域和記錄集

您可以使用 DNS SDK 搭配 .NET DNS 管理程式庫，以自動化作業來建立、刪除或更新 DNS 區域、記錄集和記錄。[這裡](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)提供完整的 Visual Studio 專案。

## NuGet 封裝和命名空間宣告

若要使用 DNS 用戶端，您必須安裝 **Azure DNS 管理程式庫** NuGet 封裝，並將 DNS 管理命名空間加入您的專案。
 
1. 在 **Visual Studio** 中，開啟專案或新專案。

2. 移至 [工具] **>** [NuGet 封裝管理員] **>** [封裝管理員主控台]。

3. 下載 Azure DNS 管理程式庫。

		using Microsoft.Azure;
		using Microsoft.Azure.Management.Dns;
		using Microsoft.Azure.Management.Dns.Models;

## 初始化 DNS 管理用戶端

*DnsManagementClient* 包含管理 DNS 區域和記錄集所需的方法和屬性。為了讓用戶端能夠存取您的訂用帳戶，您必須設定正確的權限並產生 AWT 權杖。如需詳細資訊，請參閱[驗證 Azure Resource Manager 要求](https://msdn.microsoft.com/library/azure/dn790557.aspx)。

	// get a token for the AAD application (see the article link above for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## 建立或更新 DNS 區域

若要建立 DNS 區域，需要建立 Zone 物件並傳遞給 *dnsClient.Zones.CreateOrUpdate*。因為 DNS 區域未連結到特定區域，所以位置設定為 "global"。<BR> Azure DNS 支援開放式同步存取，稱為 [Etag](dns-getstarted-create-dnszone.md)。Etag 是 Zone 的屬性。IfNoneMatch 是 ZoneCreateOrUpdateParameters 中的屬性。

	// To create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);



## 建立或更新 DNS 記錄和記錄集

DNS 記錄是以記錄集形式管理。記錄集是指一個區域內有相同名稱和記錄類型的一組記錄。若要建立或更新記錄集，需要建立 RecordSet 物件並傳遞給 dnsClient.RecordSets.CreateOrUpdate。請注意，記錄集名稱是相對於區域名稱，而不是完整的 DNS 名稱。位置設定為 "global"。<BR> Azure DNS 支援開放式同步存取 [Etag](dns-getstarted-create-dnszone.md)。Etag 是 RecordSet 的屬性。IfNoneMatch 是 RecordSetCreateOrUpdateParameters 中的屬性。
    


	// To create record sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
## 取得區域和記錄集

*Zones* 和 *RecordSets* 集合分別能夠取得區域和記錄集。RecordSets 依其類型、名稱及所在的區域和資源群組來識別。Zones 依其名稱及所在的資源群組來識別。

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

## 列出區域和記錄集

若要列出區域，請使用 Zones 集合中的 *List* 方法。若要列出記錄集，請使用 RecordSets 集合中的 *List* 或 *ListAll* 方法。*List* 方法不同於 *ListAll* 方法，因為它只會傳回指定類型的記錄集。

下列範例示範如何取得 DNS 區域和記錄集的清單：


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}


## 後續步驟

[Visual Studio SDK 範例專案](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=AcomDC_0817_2016-->