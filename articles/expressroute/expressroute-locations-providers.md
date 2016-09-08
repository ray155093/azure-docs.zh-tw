<properties
   pageTitle="ExpressRoute 位置 |Microsoft Azure"
   description="本文提供提供服務所在位置以及如何連線到 Azure 區域的詳細概觀。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="cherylmc" />

# ExpressRoute 合作夥伴和對等互連位置

本文中的資料表會提供有關 ExpressRoute 連線提供者、ExpressRoute 地理涵蓋範圍、透過 ExpressRoute 支援的 Microsoft 雲端服務，以及 ExpressRoute 系統整合者 (SI) 的資訊。

## <a name="partners"></a>ExpressRoute 連線提供者

所有的 Azure 區域和位置都支援 ExpressRoute。以下地圖提供了 Azure 區域和 ExpressRoute 位置的清單。ExpressRoute 位置是指 Microsoft 與數個服務提供者對等互連的位置。

![位置圖][0]

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

|**地緣政治區域**|**Azure 區域**|**ExpressRoute 位置**|
|---|---|---|
|**北美洲**|美國東部、美國西部、美國東部 2、美國中部、美國中南部、美國中北部、加拿大中部、加拿大東部|亞特蘭大、芝加哥、達拉斯、拉斯維加斯、洛杉磯、紐約、西雅圖、矽谷、華盛頓特區、蒙特婁+、魁北克市+、多倫多|
|**南美洲**|巴西南部|聖保羅|
|**歐洲**|北歐、西歐|阿姆斯特丹、都柏林、倫敦、紐波特 (威爾斯)+、巴黎|
|**亞洲**|東亞、東南亞|香港特別行政區、新加坡|
|**日本**|日本西部、日本東部|大阪、東京|
|**澳大利亞**|澳洲東南部、澳洲東部|墨爾本、雪梨|
|**印度**|印度西部、印度中部、印度南部|辰內，孟買|



下表提供國家雲端的區域和地理政治界限等資訊。

|**地緣政治區域**|**Azure 區域**|**ExpressRoute 位置**|
|---|---|---|---|
|**美國政府雲端**|美國政府愛荷華州、美國政府維吉尼亞州|芝加哥、達拉斯+、紐約、華盛頓特區|
|**中國**|中國北部、中國東部|北京、上海|
|**德國**|德國中部、德國東部|柏林、法蘭克福|


標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。不支援連線至國家雲端環境。如果有需要的話，您可以聯絡您的連線提供者。


## 連線提供者位置

> [AZURE.SELECTOR]
[Locations By Provider](expressroute-locations.md#connectivity-provider-locations)
[Providers By Location](expressroute-locations-providers.md#connectivity-provider-locations)

### 生產 Azure
| **位置** | **服務提供者** |
|---------------|-----------------------|
| **阿姆斯特丹** | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT+、InterCloud、Internet Solutions - Cloud Connect、Interxion、Level 3 Communications、Orange、Tata Communications、TeleCity Group、Telenor、Verizon |
| **亞特蘭大** | Equinix |
| **辰內** | Tata Communications |
| **芝加哥** | AT&T NetBond、Comcast、Equinix、Level 3 Communications、Zayo Group |
| **達拉斯** | AT&T NetBond、Equinix、Level 3 Communications、Megaport |
| **都柏林** | Colt |
| **香港** | British Telecom、China Telecom Global、Equinix、Megaport、Orange、PCCW Global Limited、Tata Communications、Verizon |
| **倫敦** | AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc+、Level 3 Communications、MTN、NTT Communications、Orange、Tata Communications、Telecity Group、Telenor、Verizon、Vodafone |
| **拉斯維加斯** | Level 3 Communications+、Megaport
| **洛杉磯** | CoreSite、Equinix、Megaport、NTT、Zayo Group |
| **墨爾本** | Equinix、Megaport、NEXTDC、Telstra Corporation |
| **紐約** | Equinix、Megaport、Zayo Group |
| **蒙特婁** | Cologix+ |
| **孟買** | Tata Communications |
| **大阪** | Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、Softbank |
| **巴黎** | Interxion |
| **聖保羅** | Equinix、Telefonica |
| **Seattle** | Equinix、Level 3 Communications、Megaport |
| **矽谷** | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink+、Comcast、Equinix、Level 3 Communications、Orange、Tata Communications、Verizon、Zayo Group |
| **新加坡** | Aryaka Networks、AT&T NetBond、British Telecom、Equinix、InterCloud、Megaport、Orange、SingTel、Tata Communications、Verizon |
| **雪梨** | AT&T NetBond、British Telecom、Equinix、Megaport、NEXTDC、Telstra Corporation、Verizon |
| **東京** | Aryaka Networks、British Telecom、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、Softbank、Verizon |
| **多倫多** | Cologix、Equinix、Zayo Group |
| **華盛頓** | Aryaka Networks、AT&T NetBond、British Telecom、Comcast、Equinix、InterCloud、Level 3 Communications、Megaport、Orange、Tata Communications、Verizon、Zayo Group |

 **+** 表示即將推出

### 國家雲端環境

#### 美國政府雲端

| **位置** |**服務提供者** |
|---------------|--------------------|
| **芝加哥** | AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **達拉斯** | Equinix+、Verizon+ |
| **紐約** | Equinix、Level 3 Communications+、Verizon |
| **華盛頓** | AT&T NetBond、Equinix、Level 3 Communications、Verizon |

#### 中國

| **位置** | **服務提供者** |
|---------------|-----------------------|
| **北京** | China Telecom |
| **上海** | China Telecom |
若要深入了解，請參閱 [ExpressRoute (中國)](http://www.windowsazure.cn/home/features/expressroute/)。

#### 德國

| **位置** | **服務提供者** |
|---------------|-----------------------|
| **柏林** | Colt+、e-shelter+ |
| **法蘭克福** | Colt、Equinix+、Interxion |

## <a name="nonpartners"></a>透過未列出的服務提供者連線

如果上一節中未列出您的連線提供者，您仍然可以建立連線。

- 請洽詢您的連線提供者，以了解他們是否連線到上方表格中列出的任何 Exchange 提供者。您可以檢查下列連結，以收集 Exchange 提供者所提供之服務的相關詳細資訊。已有數個連線提供者連線到乙太網路 Exchange。

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
	- [Cologix](http://www.cologix.com/)
- 請您的連線提供者將您的網路延伸到選擇的對等互連位置。
	- 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
- 排序一個 ExpressRoute 循環，將 Exchange 視為連線至 Microsoft 的連線提供者。
	- 依照[建立 ExpressRoute 循環](expressroute-howto-circuit-classic.md)中的步驟來設定連線。

|**位置**|**Exchange**|**連線提供者**|
|-------------|------------|-------------------------|
| **紐約** | Equinix | Lightower |
| **Seattle** | Equinix | Alaska Communications |
| **矽谷** | Equinix | XO Communications |
| **新加坡** | Equinix | 1CLOUDSTAR |
| **華盛頓** | Equinix | Lightower |

## ExpressRoute 系統整合者

根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。您可以使用下表所列出的任何系統整合者來協助您開始使用 ExpressRoute。

|**Continent**|**系統整合者**|
|-------------|---------------------|
| **亞洲** | Avanade Inc.、OneAs1a|
| **歐洲** | Avanade Inc.、Dotnet Solutions|
| **美國** | Avanade Inc.、Equinix Professional Services、Perficient、Project Leadership|

## 後續步驟

- 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
- 請確定符合所有必要條件。請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置圖"

<!---HONumber=AcomDC_0831_2016-->