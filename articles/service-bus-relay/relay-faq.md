---
title: "Azure 轉送常見問題集 (FAQ) | Microsoft Docs"
description: "回答一些有關 Azure 轉送的常見問題。"
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: jotaub;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7562fbee15e4450542c2c17d8ae2a763a556a44f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/03/2017


---
# <a name="relay-faq"></a>轉送常見問題集

本文提供 [Microsoft Azure 轉送](https://azure.microsoft.com/services/service-bus/)的一些常見問題解答。 您也可以造訪 [Azure 支援常見問題集](http://go.microsoft.com/fwlink/?LinkID=185083) ，以取得一般的 Azure 價格和支援資訊。

## <a name="general-questions"></a>一般問題
### <a name="what-is-azure-relay"></a>什麼是 Azure 轉送？
[Azure 轉送服務](relay-what-is-it.md)可執行您的混合式應用程式，方法是讓您以安全的方式向公用雲端公開位於企業網路內部的服務，而無需開啟防火牆連接或要求對企業網路基礎結構的進行侵入式變更。

### <a name="what-is-a-relay-namespace"></a>什麼是轉送命名空間？
[命名空間](relay-create-namespace-portal.md)提供範圍容器，可在應用程式內定址轉送資源。 必須建立命名空間才能使用轉送，而且這也是開始使用的第一個步驟。

### <a name="what-happened-to-the-previously-named-relay-service"></a>先前指定的轉送服務發生什麼情況？
先前稱為「服務匯流排轉送」服務現在稱為「WCF 轉送」。 您可以繼續如常使用這項服務。 「混合式連接」是從 BizTalk 移轉的服務的更新版本。 之後會繼續支援「WCF 轉送」和「混合式連接」。

## <a name="pricing"></a>價格
本節提供轉送價格結構的一些常見問題解答。 您也可以造訪 [Azure 支援常見問題集](http://go.microsoft.com/fwlink/?LinkID=185083) ，以取得一般 Microsoft Azure 價格資訊。 如需轉送價格的完整資訊，請參閱 [服務匯流排價格詳細資料](https://azure.microsoft.com/pricing/details/service-bus/)。

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>混合式連接和 WCF 轉送如何收費？
如需轉送價格的完整資訊，請參閱[服務匯流排價格詳細資料][Pricing overview]。 除了註明的價格，您還需支付您的應用程式佈建所在資料中心外部的輸出相關資料傳輸費用。

### <a name="how-am-i-billed-for-hybrid-connections"></a>混合式連接如何計費？
以下是三個範例案例：

1. 假設您有一個接聽程式 (像是安裝了混合式連接管理員)，持續執行了一整個月，而當月同期間又在連接上傳送了 3 GB 的資料，您的費用將會是美金 $5 元。
2. 假設您有一個接聽程式 (像是安裝了混合式連接管理員)，持續執行了一整個月，而當月同期間又在連接上傳送了 10 GB 的資料，您的總費用將會是美金 $7.50 元 (包括美金 $5 元的連接費用與第一個 5GB，以及其他 5 GB 的美金 $2.50 元)。
3. 假設您安裝了 A 與 B 兩個混合式連接管理員執行個體，持續執行了一整個月，而當月同期間又在連接 A 上傳送了 3 GB 的資料，在連接 B 上傳送了 6 GB 的資料，您的總費用將會是美金 $10.50 元 (包括連接 A 的美金 $5 元加連接 B 的美金 $5 元，再加上連接 B 上第 6 個 GB 起算的美金 $0.50 元)。

請注意，範例中使用的價格僅適於預覽期間。當混合式連接正式運作時可能會有所變更。

### <a name="how-are-relay-hours-calculated"></a>如何計算轉送時數？
WCF 轉送與混合式連線時數是以每個服務匯流排轉送「開啟」期間累積的時間總量計費。 當已啟用轉送的服務 (或稱「轉送接聽程式」) 第一次連接到指定的服務匯流排位址 (服務命名空間 URL) 時，轉送就會隱含具現化並於該位址開放。 只有在最後一個接聽程式從該位址中斷連接時，轉送才會關閉。 因此，為了方便計費，從第一個轉送接聽程式連接轉送的服務匯流排位址，到最後一個轉送接聽程式中斷連接的這段時間，該轉送會被視為「開放」。

只能在標準層命名空間中使用 WCF 轉送。 否則轉送的定價和[連線配額](../service-bus-messaging/service-bus-quotas.md)會保持不變。 這表示轉送將繼續按訊息數目 (而非作業數) 和轉送時數計費。 如需詳細資訊，請參閱定價詳細資料頁面上的[混合式連線與 WCF 轉送](https://azure.microsoft.com/pricing/details/service-bus/)資料表。

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>如果我有一個以上的接聽程式連接到指定的轉送，該怎麼辦？
在某些情況下，單一轉送可能有多個已連接的接聽程式。 至少有一個轉送接聽程式連接到轉送時，即會被視為「開放式」轉送。 將其他接聽程式新增至開放式轉送會導致額外的轉送時數。 連接到轉送的轉送傳送者 (叫用或傳送訊息至轉送的用戶端) 數目也不會影響轉送時數的計算。

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>如何針對 WCF 轉送計算訊息計量？
**這只適用於 WCF 轉送，而且不是混合式連線的成本**

一般而言，對代理實體 (佇列、主題和訂用帳戶) 使用上述相同方法的轉送會計算計費訊息。 但是，請注意以下幾個差異：

將訊息傳送至服務匯流排轉送會被視為「完全直達」傳送至接收訊息的轉送接聽程式，而不是傳送至服務匯流排轉送並接著傳遞至轉送接聽程式。 因此，對於轉送接聽程式的要求-回覆模式服務叫用 (最多 64 KB) 將會產生兩則計費訊息︰一則是要求的計費訊息，一則是回應的計費訊息 (假設回應也是 \<= 64 KB)。 這與使用佇列在用戶端與服務之間居中協調不同。 在後面的情況中，相同的要求-回覆模式需要將要求傳送至佇列，接著清除佇列/從佇列傳遞至服務，然後接著將回應傳送至另一個佇列，以及清除佇列/從該佇列傳遞至用戶端。 始終使用相同的 (\<= 64 KB) 大小假設，居中協調的佇列模式會因此產生四則計費訊息，這是使用轉送實作相同模式所需計費數目的兩倍。 當然，使用佇列來達成此模式有許多優點，例如，持久性和負載調節。 這些優點可合理解釋額外的費用。

使用 netTCPRelay WCF 繫結開啟的轉送不會將訊息視為個別的訊息，而會視為通過系統的資料流。 換句話說，只有傳送者和接聽程式能夠看見使用此繫結傳送/接收之個別訊息的框架。 因此，對於使用 netTCPRelay 繫結的轉送，所有資料都會被視為資料流，以便計算計費訊息。 在此情況下，服務匯流排會計算每隔 5 分鐘透過每個個別轉送傳送或接收的資料總量並將該總量除以 64 KB，以便判斷該期間內有問題之轉送的計費訊息數目。

## <a name="quotas"></a>配額
| 配額名稱 | Scope | 類型 | 超出時的行為 | 值 |
| --- | --- | --- | --- | --- |
| 轉送上的並行接聽程式 |實體 |靜態 |後續對更多連接的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |25 |
| 並行轉送接聽程式 |全系統 |靜態 |後續對更多連接的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |2,000 |
| 服務命名空間中所有轉送端點的並行轉送連線 |全系統 |靜態 |- |5,000 |
| 每個服務命名空間的轉送端點 |全系統 |靜態 |- |10,000 |
| [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) 和 [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) 轉送的訊息大小 |全系統 |靜態 |超出這些配額的內送訊息將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |64KB |
| [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) 和 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 轉送的訊息大小 |全系統 |靜態 |- |無限制 |

### <a name="does-relay-have-any-usage-quotas"></a>轉送是否有任何使用量配額？
根據預設，對於所有雲端服務，Microsoft 會設定針對所有客戶的訂用帳戶計算的彙總每月使用量配額。 因為我們了解您的需求可能超過這些限制，請隨時連絡客戶服務部門，讓我們能夠了解您的需求並適當地調整這些限制。 服務匯流排的彙總使用量配額如下：

* 50 億則訊息
* 2 百萬個轉送小時

雖然我們有權停用在指定的月份內超出其使用量配額的客戶帳戶，但我們將提供電子郵件通知並且在採取任何動作之前多次嘗試連絡客戶。 超出這些配額的客戶仍需負責支付超出配額的費用。

### <a name="naming-restrictions"></a>命名限制
轉送命名空間的名稱長度只能介於 6 到 50 個字元之間。

## <a name="subscription-and-namespace-management"></a>訂用帳戶和命名空間管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>如何將命名空間移轉到另一個 Azure 訂用帳戶？
您可以使用 PowerShell 命令 (可在[這裡](../service-bus-messaging/service-bus-powershell-how-to-provision.md)的文章中找到) 將命名空間從某個 Azure 訂用帳戶移到另一個 Azure 訂用帳戶。 若要執行此作業，命名空間必須已是作用中。 此外，執行命令的使用者也必須是來源和目標訂用帳戶的系統管理員。

## <a name="troubleshooting"></a>疑難排解
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Azure 轉送 API 所產生的例外狀況有哪些，其建議的動作為何？
[轉送例外狀況][Relay exceptions]一文會說明部分例外狀況和建議的動作。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>什麼是共用存取簽章，何種語言可支援產生簽章？
共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。 如需如何以 Node、PHP、Java 和 C\# 產生自有簽章的相關資訊，請參閱[共用存取簽章][Shared Access Signatures]一文。

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas-overview.md

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>可以將轉送端點列入白名單嗎？
是。 轉送用戶端會使用完整的網域名稱連線至轉送服務。 這可讓客戶在防火牆上新增 `*.servicebus.windows.net` 項目以支援 DNS 白名單。

## <a name="next-steps"></a>後續步驟
* [建立命名空間](relay-create-namespace-portal.md)
* [開始使用 .NET](relay-hybrid-connections-dotnet-get-started.md)
* [開始使用 Node](relay-hybrid-connections-node-get-started.md)

