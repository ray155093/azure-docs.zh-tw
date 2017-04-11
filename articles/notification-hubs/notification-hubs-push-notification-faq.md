---
title: "Azure 通知中樞 - 常見問題集 (FAQ)"
description: "在通知中樞上設計/實作解決方案的常見問題集"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "推播通知, 推播通知, iOS 推播通知, android 推播通知, ios 推播, android 推播"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f0e7f071f1aa8fdd95c4959eae884afc60644e9
ms.lasthandoff: 03/25/2017


---
# <a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>使用 Azure 通知中樞推播通知 - 常見問題集 (FAQ)
## <a name="general"></a>一般
### <a name="0--what-is-the-resource-structure-of-notification-hubs"></a>0.通知中樞的資源結構為何？

通知中樞有兩個資源層級，分別是中樞和命名空間。 中樞是單一推播資源，可包含一個應用程式的跨平台推播資訊。 命名空間是一個區域中多個中樞的集合。

一般的建議對應會放置一個命名空間來與一個應用程式配對。 在命名空間內，您可以有一個生產中樞來與生產應用程式搭配運作，以及一個測試中樞來與測試應用程式搭配運作，依此類推。

### <a name="1--what-is-the-price-model-for-notification-hubs"></a>1.「通知中樞」的價格模式為何？
如需最新的詳細資訊，請參閱 [通知中樞價格] 頁面。 通知中樞會在命名空間層級計費 (請參閱上面的資源結構以了解命名空間為何)，並提供三個層級︰

* **免費** - 這是適合作為瀏覽推播功能的起點，但不建議用於生產應用程式。 此層級每月可在每個命名空間中包含 500 個裝置和 100 萬個推播，但沒有 SLA 保證。
* **基本** - 較小型的生產應用程式建議使用這個層級或標準層級。 此層級基本上每月可在每個命名空間中包含 20 萬個裝置和 1000 萬個推播，並有增加配額的選項。
* **標準** - 中大型的生產應用程式建議使用此層級。 此層級基本上每月可在每個命名空間中包含 1000 萬個裝置和 1000 萬個推播，並有增加配額的選項，以及豐富的遙測功能。

以下是一些不錯的標準層級功能︰
* *豐富的遙測* - 通知中樞提供依訊息遙測來追蹤任何推播要求和要偵錯的平台通知系統意見反應。
* *多租用戶* - 您可以在命名空間層級上使用平台通知系統 (PNS) 認證。 這可讓您在相同的命名空間內輕鬆地將租用戶分割成多個中樞。
* *排定推播* - 您可以排程在任何時間傳送通知。

### <a name="2--what-is-the-notification-hubs-sla"></a>2.什麼是通知中樞 SLA？
對於**「基本」**與**「標準」**通知中樞層次，我們保證已正確設定的應用程式至少有 99.9% 的時間可以傳送推播通知或執行註冊管理作業 (對於部署在支援的層次內的通知中樞)。 若要深入了解我們的 SLA，請瀏覽 [通知中樞 SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) 頁面。

> [!NOTE]
> 因為推播通知依存於第 3 方平台通知系統 (Apple 的 APNS、Google 的 FCM 等)，因此這些訊息的傳遞沒有 SLA 保證。 通知中樞將傳送作業批次送到 PNS (有 SLA 保證) 後，便會由 PNS 傳遞推播 (沒有 SLA 保證)。

### <a name="3--which-customers-are-using-notification-hubs"></a>3.客戶如何使用通知中樞？
我們有大量使用「通知中樞」的客戶，以下列出部分較知名者：

* 索契 2014 – 100 個以上的興趣群組、3 百萬部以上的裝置、1.5 億則以上的通知 (在 2 週內傳送)。 [案例研究 - 索契]
* Skanska - [案例研究 - Skanska]
* Seattle Times - [案例研究 - Seattle Times]
* Mural.ly - [案例研究 - Mural.ly]
* 7Digital - [案例研究 - 7Digital]
* Bing 應用程式 – 1 千萬部以上的裝置、每日傳送 3 百萬則通知。

### <a name="4-how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>4.如何將中樞或命名空間升級或降級為不同層級？
移至 [Azure 入口網站]通知中樞命名空間或通知中樞，按一下您想要更新的資源，然後移至導覽上的 [定價層]。 您可以更新為任何您想要的層級，但有一些注意事項︰
* 更新後的定價層會套用至您正在使用之命名空間中的「所有」中樞。
* 如果您要降級，而裝置數超過您嘗試要降級為之層級的限制，則必須先刪除裝置以符合限制，然後才能降級。


## <a name="design--development"></a>設計與開發
### <a name="1--which-server-side-platforms-do-you-support"></a>1.支援哪些伺服器端平台？
我們有適用於 .NET、Java、Node.js、PHP 和 Python 的伺服器 SDK。 此外，通知中樞 API 是以 REST 介面為根據，因此如果您要使用不同平台或不想要額外的相依性，則可以選擇直接使用 REST API。 您可以在 [NH - REST APIs] 頁面上看到更多詳細資料。

### <a name="2--which-client-platforms-do-you-support"></a>2.支援哪些用戶端平台？
我們支援將推播通知傳送到 [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md)、[Android](notification-hubs-android-push-notification-google-gcm-get-started.md)、[Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)、[Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md)、[Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)、[Android China (由百度開發)](notification-hubs-baidu-china-android-notifications-get-started.md)、Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md))、[Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) 和 [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari)。 如需詳細資訊，請參閱 [使用者入門教學課程] 頁面。

### <a name="3--do-you-support-smsemailweb-notifications"></a>3.是否支援簡訊/電子郵件/Web 通知？
「通知中樞」主要設計來傳送通知給行動裝置應用程式，並未提供電子郵件或簡訊功能。 但是您可以將提供這些功能的第三方平台與「通知中樞」整合，以使用 [Azure Mobile Apps] 來傳送原生推播通知。

通知中樞也不提供現成可用的瀏覽器中推播通知傳送服務。 客戶可以在支援的伺服器端平台上，選擇使用 SignalR 來實作此功能。 如果您想要在 Chrome 沙箱中將通知傳送至瀏覽器應用程式，請參閱 [Chrome 應用程式教學課程]。

### <a name="4----what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4.  Azure Mobile Apps 與 Azure 通知中樞之間的關係為何以及何時使用？
如果您有現有的行動裝置應用程式後端，而且您只想要新增傳送推播通知的功能，則您可以使用 Azure 通知中樞。 如果您想要從頭開始設定您的行動裝置應用程式後端，則應該考慮使用 Azure Mobile Apps。 Azure Mobile Apps 會自動為您佈建「通知中樞」，讓您能夠輕鬆地從行動裝置應用程式後端傳送推播通知。 Azure Mobile Apps 的價格包括「通知中樞」的基本費率，因此您只需要在超過包含的推播數時才需要付費。 您可以在 [App Service 價格] 上取得成本的詳細資料。

### <a name="5----how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5.  如果透過通知中樞傳送推播通知，可以支援多少個裝置？
如需支援的裝置數目詳細資訊，請參閱 [通知中樞價格] 頁面。

在特定案例中，如果您需要支援超過 10,000,000 個已註冊的裝置，請直接 [與我們連絡](https://azure.microsoft.com/overview/contact-us/) ，我們將協助您調整您的解決方案。

### <a name="6----how-many-push-notifications-can-i-send-out"></a>6.  我可以傳送多少推播通知？
端視選取的層次而定，Azure 會自動根據通知數目相應增加透過系統傳送的通知數目。

> [!NOTE]
> 整體使用成本可能會根據目前提供的推播通知數目而增加。 請務必留意 [通知中樞價格] 頁面上概述的現有層次限制。
> 
> 

我們現有的客戶每天使用通知中樞來傳送數百萬則推播通知。 只要使用 Azure 通知中樞，您就不需要採取任何特別動作來調整推播通知可及範圍。

### <a name="7----how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7.  送出的推播通知多久才會到達我的裝置？
在一般使用案例中，「Azure 通知中樞」能在 1 分鐘處理至少 1 百萬個推播通知傳送作業  ，能處理的連入負載數目大致上差不多，而且在本質上並不難處理。 此速率可能視標記數目、連入傳送作業本質和其他外部因子而異。

在預估的傳遞時間內，服務能計算每個平台的目標數，並根據已註冊的標記/標記運算式將訊息路由傳送到對應的推播通知傳送服務。 從這裡開始，傳送通知到裝置是推播通知服務 (PNS) 的責任。

PNS 不會為傳送通知提供任何 SLA 保證；不過一般而言，絕大部分的推播通知會在傳送到我們平台之後的數分鐘 (通常在 10 分鐘的限制內) 內傳送到目標裝置。 可能有少數異常情況會花比較多的時間。

> [!NOTE]
> 「Azure 通知中樞」有一個原則，可捨棄無法在 30 分鐘內傳送到 PNS 的任何推播通知。 發生此延遲的原因有數種，最常見的原因是 PNS 正在為您的應用程式進行節流處理。
> 
> 

### <a name="8----is-there-any-latency-guarantee"></a>8.  是否有任何延遲保證？
因為推播通知的本質 (由外部平台特定「推播通知服務」所傳送)，我們不提供延遲保證。 一般而言，大部分的推播通知會在數分鐘內傳送到目標裝置。

### <a name="9----what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9.  設計具有命名空間與「通知中樞」的解決方案時，必須考量哪些事項？
#### <a name="mobile-appenvironment"></a>行動裝置應用程式/環境
* 每個環境的每個行動裝置應用程式都應該有一個「通知中樞」。
* 在多租用戶案例中，每個租用戶都應該有個別的中樞。
* 您永遠不應該在測試與生產環境之間共用相同的「通知中樞」，因為這可能導致傳送通知時發生問題。 例如 Apple 提供「沙箱」與「實際執行推播」端點，而這兩者有個別的認證。
* 根據預設，您可以透過 Azure 入口網站或 Visual Studio 中的 Azure 整合式元件，傳送測試通知到已註冊的裝置。 臨界值會設定為從註冊集區中隨機選取的 10 個裝置。

> [!NOTE]
> 如果一開始使用 Apple 沙箱憑證設定中樞，接著重新設定為使用 Apple 實際執行憑證，則舊的裝置權杖會成為無效且無法搭配新的憑證使用，並導致推播失敗。 建議您最好將實際執行與測試環境分開，並針對不同的環境使用不同的中樞。
> 
> 

#### <a name="pns-credentials"></a>PNS 認證
當行動裝置應用程式向平台的開發人員入口網站 (例如 Apple 或 Google 等) 註冊時，您會取得應用程式識別碼與安全性權杖，應用程式後端必須將其提供給平台的「推播通知」服務，才能傳送推播通知給裝置。 您必須在「通知中樞」中設定這些安全性權杖，其形式可以是憑證 (例如，對於 Apple iOS 或 Windows Phone) 或安全性金鑰 (Google Android、Windows 等)。 此動作通常是在通知中樞層級完成，但在多組織用戶共享情節中，這也可以在命名空間層級完成。

#### <a name="namespaces"></a>命名空間
命名空間可以用於部署分組。  在多租用戶案例中，它也可以用來代表相同應用程式之所有租用戶的所有通知中樞。

#### <a name="geo-distribution"></a>地理分散
在推播通知案例中，地理分散不一定是關鍵。 請注意，最終會將推播通知傳送到裝置的各種推播通知服務 (例如 APNS 與 GCM 等) 也不會平均分散。

如果您的應用程式是提供給全球各地的使用者使用，您可以在不同的命名空間中建立數個中樞，以利用全球不同 Azure 區域中「通知中樞」服務的可用性優點。

> [!NOTE]
> 這樣會增加管理成本 (特別是有關註冊方面)，因此建議您只有在有明確需求時才這樣做。
> 
> 

### <a name="10----should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10.  應該從應用程式後端註冊或直接透過用戶端裝置註冊？
當您必須在建立註冊之前執行用戶端驗證，或您有必須由應用程式後端根據某些應用程式邏輯建立或修改的標記時，從應用程式後端註冊非常實用。 如需詳細資訊，您可以進一步了解[後端註冊指引]與[後端註冊指引 - 2] 頁面。

### <a name="11----what-is-the-push-notification-delivery-security-model"></a>11.  什麼是推播通知傳送資訊安全模型？
Azure 通知中樞使用[共用存取簽章 (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md) 型資訊安全模型。 您可以在根命名空間層級或更細微的通知中樞層級使用 SAS 權杖。 您可以使用不同的授權規則設定這些 SAS 權杖，例如傳送訊息權限與接聽通知權限等。如需詳細資料，請參閱 [NH 資源安全模型]文件。

### <a name="12----how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12.  應如何處理推播通知中的機密承載？
通知是由平台的推播通知服務 (PNS) 傳送至目標裝置。 當傳送者將通知傳送給「Azure 通知中樞」時，我們會接著處理通知並將它傳送給個別的 PNS。

傳送者與「Azure 通知中樞」之間的連線以及「Azure 通知中樞」與 PNS 之間的連線都使用 HTTPS。

> [!NOTE]
> 「Azure 通知中樞」不會以任何方式記錄訊息的承載。
> 
> 

若要傳送機密承載，建議您使用「安全推播」模式，其中傳送者會傳送 'ping' 通知與訊息識別碼給裝置 (不含機密承載)，而當裝置上的應用程式收到此承載時，它就能夠直接呼叫安全 API 以擷取訊息詳細資料。 您可以在 [NH - 安全推播教學課程] 頁面上取得如何實作上述模式的指南。

## <a name="operations"></a>作業
### <a name="1----what-is-the-disaster-recovery-dr-story"></a>1.  災害復原 (DR) 是什麼？
我們在我們這邊提供中繼資料災害復原範圍 (通知中樞名稱、連接字串和其他重要資訊)。 觸發 DR 案例後，註冊資料是通知中樞基礎結構中唯一會遺失的區段  。 您必須實作解決方案，將此資料重新填入到復原後的新中樞。

* *步驟 1* - 在不同的資料中心建立次要通知中樞。 您可以在發生 DR 事件時建立此通知中樞，或一開始就建立。 您選擇哪一個選項並沒有很大的差別。因為通知中樞佈建是相當快速的程序，大約只需要數秒的時間。 一開始就建立通知中樞將能避免 DR 事件影響我們的管理功能，因此強烈建議您這樣做。
* *步驟 2* - 將來自主要通知中樞的註冊移往次要通知中樞。 建議您不要嘗試在兩個中樞上維護註冊，而應該在收到註冊時進行同步，因為註冊會在 PNS 端到期的固有傾向，第一種方式通常無法正確運作。 當我們收到有關已到期或無效註冊的 PNS 回應時，「通知中樞」會將它們清除。  

建議您使用會執行下列任一動作的應用程式後端：

* 在本身維護一組指定的註冊，以便它可以在發生 DR 時執行大量插入作業以將資料插入到次要通知中樞

**OR**

* 定期從主要中樞傾印註冊做為備份，然後執行大量插入作業以將資料插入到次要 NH。

> [!NOTE]
> [註冊匯出/匯入] 文件說明標準層中提供的註冊匯出/匯入功能。
> 
> 

若您沒有後端，則當應用程式在任何目標裝置上啟動時，它們將會在次要通知中樞中執行新註冊，而最後次要通知中樞將會有所有已註冊的使用中裝置。

缺點是裝置上的應用程式未開啟期間將不會收到通知。

### <a name="2----is-there-any-audit-log-capability"></a>2.  是否有任何稽核記錄功能？
所有通知中樞管理作業移至作業記錄，其公開於 [Azure 傳統入口網站]中。

## <a name="monitoring--troubleshooting"></a>監視和疑難排解
### <a name="1----what-troubleshooting-capabilities-are-available"></a>1.  可用的疑難排解功能有哪些？
Azure 通知中樞提供數個功能，可進行常見的疑難排解，特別是在已捨棄通知的最常見案例中。 請參閱 [NH - 疑難排解] 白皮書中的詳細資料。

### <a name="2----what-telemetry-features-are-available"></a>2.  可用的遙測功能有哪些？
您可透過 Azure 通知中樞，在 [Azure 傳統入口網站]中檢視遙測資料。 您可以在 [NH - 計量] 頁面上可取得計量的詳細資料。

> [!NOTE]
> 成功的通知只表示推播通知已傳送到外部推播通知服務 (例如 Apple 的 APNS 與 Google 的 GCM 等)。 由 PNS 負責將通知傳送至目標裝置。 PNS 通常不會向第三方公開計量。  
> 
> 

我們還提供以程式設計方式匯出遙測資料的功能 (在「標準」  層次中)。 如需詳細資料，請參閱 [NH - 計量範例] 。

[Azure 傳統入口網站]: https://manage.windowsazure.com
[通知中樞價格]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[案例研究 - 索契]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[案例研究 - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[案例研究 - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[案例研究 - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[案例研究 - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - REST APIs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Getting Started Tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome 應用程式教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[後端註冊指引]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[後端註冊指引 - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH 資源安全模型]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - 安全推播教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - 疑難排解]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - 計量]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - 計量範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[註冊匯出/匯入]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure 入口網站]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service 價格]: https://azure.microsoft.com/pricing/details/app-service/

