---
title: "Azure 媒體服務概觀和常見案例 | Microsoft Docs"
description: "本主題提供 Azure 媒體服務的概觀"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/29/2017
ms.author: juliako;anilmur
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 42baa7a6e80468e47dda1e652fc2bbd20e5a4ee6
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017


---
<a id="azure-media-services-overview-and-common-scenarios" class="xliff"></a>

# Azure 媒體服務概觀和常見案例

Microsoft Azure 媒體服務是一個可延伸的雲端型平台，供開發人員建置可擴充的媒體管理和傳遞應用程式。 媒體服務是以 REST API 為基礎，可讓您安全地上傳、儲存、編碼和封裝視訊或音訊內容，以用於隨選和即時資料流傳遞給各種用戶端 (例如電視、電腦和行動裝置)。

您可以建置完全採用媒體服務的端對端工作流程。 您也可以選擇在工作流程的某些部分採用第三方元件。 例如，使用第三方編碼器來進行編碼； 然後使用媒體服務上傳、保護、封裝、傳遞。

您可以選擇即時串流您的內容或隨選傳遞內容。 本主題說明[即時](media-services-overview.md#live_scenarios)傳遞內容或[隨選](media-services-overview.md#vod_scenarios)傳遞內容的常見案例。 本主題也會連結到其他相關主題。

<a id="sdks-and-tools" class="xliff"></a>

## SDK 及工具

若要建置媒體服務解決方案，您可以使用：

* [媒體服務 REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* 其中一個可用的用戶端 SDK：
    * [Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、
    * [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)、
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)、
    * [Azure Media Services for Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (這是非 Microsoft 版本的 Node.js SDK。 它是由社群維護，且目前並沒有 AMS API 的 100% 涵蓋範圍)。
* 現有的工具：
    * [Azure 入口網站](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure 媒體服務總管 (AMSE) 是適用於 Windows 的 Winforms/C# 應用程式)

下列影像顯示針對媒體服務 OData 模型進行開發時一些最常用的物件。

按一下影像可以完整大小檢視。  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

您可以[在此](https://media.windows.net/API/$metadata?api-version=2.15)檢視整個模型。  

<a id="media-services-learning-paths" class="xliff"></a>

## 媒體服務學習路徑
您可以在此檢視 AMS 學習路徑：

* [AMS 即時資料流工作流程](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [AMS 隨選資料流工作流程](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<a id="prerequisites" class="xliff"></a>

## 必要條件

若要開始使用 Azure 媒體服務，您應該具備下列項目：

1. 一個 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com)。
2. Azure 媒體服務帳戶。 使用 Azure 入口網站、.NET 或 REST API 建立 Azure 媒體服務帳戶。 如需詳細資訊，請參閱[建立帳戶](media-services-portal-create-account.md)。
3. (選擇性) 設定開發環境。 針對開發環境選擇 .NET 或 REST API。 如需詳細資訊，請參閱 [設定環境](media-services-dotnet-how-to-use.md)。

    此外，請了解如何[以程式設計方式連線](media-services-dotnet-connect-programmatically.md)。
4. 已啟動狀態的標準或進階串流端點。  如需詳細資訊，請參閱[管理串流端點](https://docs.microsoft.com/en-us/azure/media-services/media-services-portal-manage-streaming-endpoints)

<a id="concepts-and-overview" class="xliff"></a>

## 概念和概觀
如需 Azure 媒體服務概念，請參閱 [概念](media-services-concepts.md)。

如需介紹 Azure 媒體服務的所有主要元件的操作說明系列，請參閱 [Azure 媒體服務逐步教學課程](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series)。 這一系列有絕佳的概念概觀，並使用 AMSE 工具來示範 AMS 工作。 AMSE 工具是一種 Windows 工具。 這項工具支援大部分可以使用 [AMS SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services)、[Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java) 或 [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php) 以程式設計方式完成的工作。

## <a id="vod_scenarios"></a>使用 Azure 媒體服務傳遞點播媒體：常見案例和工作
本節描述常見的案例並提供相關主題的連結。 下圖顯示在傳遞隨選內容中牽涉到的媒體服務平台的主要部分。

![VoD 工作流程](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

>[!NOTE]
>建立 AMS 帳戶時，**預設**串流端點會新增至 [已停止] 狀態的帳戶。 若要開始串流內容並利用動態封裝和動態加密功能，您想要串流內容的串流端點必須處於 [執行中] 狀態。

<a id="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted" class="xliff"></a>

### 保護儲存體中的內容並提供不加密的串流媒體
1. 將高品質夾層檔上傳到資產。

    建議您將儲存體加密選項套用到資產，以便內容在上傳時以及在儲存體中存放時可以得到保護。
2. 編碼成一組調適性位元速率 MP4 檔案。

    建議您將儲存體加密選項套用到輸出資產，以便保護存放的內容。
3. 設定資產傳遞原則 (用於動態封裝)。

    如果您的資產是已加密的儲存體， **必須** 設定資產傳遞原則。
4. 藉由建立 OnDemand 定位器的方法來發行資產。
5. 串流發佈的內容。

<a id="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media" class="xliff"></a>

### 保護儲存體中的內容、提供動態加密串流處理媒體

1. 將高品質夾層檔上傳到資產。 將儲存體加密選項套用到資產。
2. 編碼成一組調適性位元速率 MP4 檔案。 將儲存體加密選項套用到輸出資產。
3. 針對您想要在播放期間動態加密的資產，建立加密內容金鑰。
4. 設定內容金鑰授權原則。
5. 設定資產傳遞原則 (供動態封裝和動態加密使用)。
6. 藉由建立 OnDemand 定位器的方法來發行資產。
7. 串流發佈的內容。

<a id="use-media-analytics-to-derive-actionable-insights-from-your-videos" class="xliff"></a>

### 使用媒體分析從您的視訊衍生可採取行動的見解
媒體分析是一組口說與視覺元件，組織或企業可利用它，從其影片檔輕鬆製作能採取行動的見解。 如需詳細資訊，請參閱 [Azure 媒體服務分析概觀](media-services-analytics-overview.md)。

1. 將高品質夾層檔上傳到資產。
2. 使用下列其中一項媒體分析服務來處理您的視訊︰

   * **索引器** – [使用 Azure 媒體索引器 2 處理視訊](media-services-process-content-with-indexer2.md)
   * **Hyperlapse** – [Hyperlapse Media 檔案與 Azure Media Hyperlapse](media-services-hyperlapse-content.md)
   * **動作偵測** – [Azure 媒體分析的動作偵測](media-services-motion-detection.md)。
   * **臉部偵測和臉部情緒** – [Azure 媒體分析的臉部和情緒偵測](media-services-face-and-emotion-detection.md)。
   * **視訊摘要** – [使用 Azure 媒體視訊縮圖建立視訊摘要](media-services-video-summarization.md)
3. 媒體分析的媒體處理器會產生 MP4 檔案或 JSON 檔案。 如果媒體處理器產生了 MP4 檔案，您可以漸進式下載檔案。 如果媒體處理器產生了 JSON 檔案，您可以從 Azure Blob 儲存體下載檔案。

<a id="deliver-progressive-download" class="xliff"></a>

### 提供漸進式下載
1. 將高品質夾層檔上傳到資產。
2. 編碼成單一 MP4 檔案。
3. 藉由建立 OnDemand 或 SAS 定位器的方法來發行資產。

    如果使用 SAS 定位器，內容是從 Azure blob 儲存體下載的。 在此情況下，您不需要有已啟動狀態的串流端點。
4. 漸進式下載內容。

## <a id="live_scenarios"></a>使用 Azure 媒體服務傳遞即時串流事件
使用即時資料流時通常涉及下列元件：

* 相機，用來廣播事件。
* 即時視訊編碼器，它會將相機中的訊號轉換成資料流，然後再傳送至即時資料流服務。

(選擇性) 多個即時同步處理的編碼器。 針對某些需要相當高度可用性與高品質經驗的重要即時事件，建議使用主動對主動備援編碼器搭配時間同步處理，以便順利進行容錯移轉，而不會遺失資料。

* 即時串流服務可讓您執行下列動作：
* 使用各種即時串流處理通訊協定 (例如 RTMP 或 Smooth Streaming) 擷取即時內容，
* (選擇性) 將您的串流編碼成調適性位元速率串流
* 預覽您的即時串流，
* 記錄並儲存擷取的內容以於稍後進行串流 (隨選視訊)
* 透過一般串流通訊協定 (例如，MPEG DASH、Smooth、HLS) 直接將內容傳遞給客戶，或傳遞至內容傳遞網路 (CDN) 供進一步的發佈。

**Microsoft Azure 媒體服務** (AMS) 提供擷取、編碼、預覽、儲存和傳遞即時串流內容的能力。

當您將內容傳遞給客戶時，您的目標是在不同的網路條件下將高品質的視訊傳遞到各種裝置。 請注意品質和網路狀況，使用即時編碼器將您的串流編碼成多位元速率 (調適性位元速率) 視訊串流。  請注意不同裝置上的串流，使用媒體服務 [動態封裝](media-services-dynamic-packaging-overview.md) 將串流動態地重新封裝至不同的通訊協定。 媒體服務支援傳遞下列自適性串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH。

在 Azure 媒體服務中，**通道**、**程式**及 **StreamingEndpoints** 會處理所有的即時串流功能，包括內嵌、格式化、DVR、安全性、延展性和備援能力。

在 Azure 媒體服務中， **通道** 代表處理即時串流內容的管線。 通道可以用下列方式接收即時輸入串流：

* 內部部署即時編碼器會傳送多位元速率 **RTMP** 或 **Smooth Streaming** (分散的 MP4) 到針對**即時通行**傳遞所設定的通道。 **即時通行**傳遞就是擷取的串流會通過**通道**，無需任何進一步的轉碼和編碼。 您可以使用下列輸出多位元速率 Smooth Streaming 的即時編碼器：MediaExcel、Imagine Communications、Ateme、Envivio、Cisco 和 Elemental。 下列即時編碼器會輸出 RTMP：Adobe Flash Live Encoder、Haivision、Telestream Wirecast、Teradek 和 Tricaster 編碼器。  即時編碼器也會將單一位元速率串流傳送至無法用於即時編碼的通道，但是不建議您使用此方法。 接到要求時，媒體服務會傳遞串流給客戶。

> [!NOTE]
> 如果您在很長一段時間內進行多個事件，而且已投資內部部署編碼器時，使用傳遞方法是進行即時串流的最經濟實惠方式。 請參閱 [價格](https://azure.microsoft.com/pricing/details/media-services/) 詳細資料。
>
>

* 內部部署即時編碼器會傳送單一位元速率串流至通道，可以使用下列格式之一，以媒體服務執行即時編碼：RTP (MPEG-TS)、RTMP 或 Smooth Streaming (分散的 MP4) 。 通道接著會執行即時編碼，將連入的單一位元速率串流編碼成多位元速率 (自動調整) 視訊串流。 接到要求時，媒體服務會傳遞串流給客戶。

<a id="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through" class="xliff"></a>

### 使用可從內部部署編碼器接收多位元速率即時串流的通道 (即時通行)
下圖顯示 **即時通行** 工作流程中涉及的 AMS 平台主要部分。

![即時工作流程][live-overview2]

如需詳細資訊，請參閱 [使用通道，從內部部署編碼器接收多位元速率即時串流](media-services-live-streaming-with-onprem-encoders.md)。

<a id="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services" class="xliff"></a>

### 使用啟用的通道來以 Azure 媒體服務執行即時編碼
下圖顯示 AMS 平台的主要部分，與通道可以使用媒體服務執行即時編碼的即時串流工作流程有關。

![即時工作流程][live-overview1]

如需詳細資訊，請參閱 [使用啟用的通道來以 Azure 媒體服務執行即時編碼](media-services-manage-live-encoder-enabled-channels.md)。

<a id="supported-media-processors" class="xliff"></a>

## 支援媒體處理器

|名稱|狀態|資料中心
|---|---|---|
|Azure 媒體臉部偵測器|預覽|全部|
|Azure Media Hyperlapse|預覽|全部|
|Azure Media Indexer|GA|全部|
|Azure 媒體動作偵測器|預覽|全部|
|Azure 媒體 OCR|預覽|全部|
|Azure 媒體Media Redactor|預覽|全部|
|Azure Media Stabilizer|預覽|全部|
|Azure 媒體視訊縮圖|預覽|全部|
|Media Encoder Standard|GA|全部|
|Media Indexer v2|預覽|所有區域 (中國和美國聯邦政府區域除外)|
|媒體編碼器高階工作流程|GA|所有區域 (中國除外)|

<a id="consuming-content" class="xliff"></a>

## 使用內容
Azure 媒體服務提供一些工具，供您用來建立適用於大部分平台的豐富、動態用戶端播放器應用程式，此處所述的平台包括：iOS 裝置、Android 裝置、Windows、Windows Phone、Xbox 和機上盒。 下列主題也會提供 SDK 和 Player Framework 連結，可讓您開發自己的用戶端應用程式，使用來自媒體服務的串流媒體。

[開發視訊播放器應用程式](media-services-develop-video-players.md)

<a id="enabling-azure-cdn" class="xliff"></a>

## 啟用 Azure CDN
媒體服務支援與 Azure CDN 整合。 如需如何啟用 Azure CDN 的資訊，請參閱 [如何管理媒體服務帳戶中的串流端點](media-services-portal-manage-streaming-endpoints.md)。

<a id="scaling-a-media-services-account" class="xliff"></a>

## 調整媒體服務帳戶

您可以指定要佈建給帳戶的**串流保留單元**和**編碼保留單元**，藉以調整**媒體服務**。

您也可以透過新增儲存體帳戶，來調整媒體服務帳戶。 每個儲存體帳戶的限制為 500 TB。 若想將儲存空間擴大為超過預設限制，您可以選擇將多個儲存體帳戶附加至單一媒體服務帳戶。
媒體服務客戶可根據其需求，選擇一個**標準**串流端點，或選擇一或多個**進階**串流端點。 大多數的串流工作負載都適合使用標準串流端點。 其所含的功能與進階串流單位相同。 如果您的進階工作負載或串流容量需求不符合標準串流端點輸送量目標，或您想要控制 StreamingEndpoint 服務的容量，藉由調整縮放單位 (也稱為進階串流單位) 來處理不斷成長的頻寬需求，則建議配置縮放單位。

[本主題](media-services-portal-scale-streaming-endpoints.md) 會連結相關主題。

<a id="support" class="xliff"></a>

## 支援
[Azure 支援](https://azure.microsoft.com/support/options/) 提供 Azure 的支援選項，包括媒體服務。

<a id="provide-feedback" class="xliff"></a>

## 提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<a id="service-level-agreement-sla" class="xliff"></a>

## 服務等級協定 (SLA)
* 對於媒體服務編碼，我們保證 REST API 交易可用性高達 99.9%。
* 對於串流，我們在購買一個標準或近界串流端點時，針對現有的媒體內容，可保證以 99.9% 的可用性成功服務要求。
* 對於即時通道，我們保證執行中的通道至少 99.9% 的時間具有外部連線能力。
* 對於內容保護，我們保證至少 99.9% 的時間將會成功滿足金鑰要求。
* 對於索引子，我們將會在 99.9% 的時間成功服務編碼保留單元處理的索引子工作要求。

如需詳細資訊，請參閱 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)。

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png

