---
title: "透過 Azure CDN 的媒體串流最佳化"
description: "將串流媒體檔案最佳化以便傳遞順暢"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 582eb69ccd2830643644760753cca1c96d72128d
ms.contentlocale: zh-tw
ms.lasthandoff: 06/28/2017

---
# <a name="media-streaming-optimization-via-azure-cdn"></a>透過 Azure CDN 的媒體串流最佳化 
 
高畫質影片在網際網路中日益增多，其對於透過網際網路有效傳遞這類大型檔案造成各種難題。 客戶期待在世界各地的各種網路和用戶端上順暢播放 VOD 或即時影片資產。 為媒體串流檔案提供更快速且有效的傳遞機制，對於確保順暢且愉悅的取用者經驗極為重要。  

即時串流媒體特別難以傳遞，不僅是因為大尺寸和並行檢視者的數目，也因為延遲是使用者的致命傷。 即時資料流無法預先進行快取，而且檢視者無法接受長時間延遲，因此必須及時傳遞影片片段。 

串流的要求模式也帶來一些新挑戰。 不論是熱門的即時資料流，或是針對點播視訊 (VOD) 發行新的序列時，都可能有數千到數百萬個檢視者嘗試在同一時間要求資料流。 在此情況下，智慧要求彙總極為重要，因為在尚未快取資產時，才不會讓原始伺服器超過負荷。
 
Azure CDN from Akamai 現在提供一項功能，能夠大規模而有效率地將串流媒體資產提供給全球使用者，並且減少延遲，同時降低原始伺服器的負載。 透過在 Azure CDN 設定檔下使用「標準 Akamai」定價層建立之 Azure CDN 端點上的 [最佳化] 功能，即可取得這項功能。 您應該對 VOD 資產使用「點播視訊媒體串流」最佳化，而如果您組合即時和 VOD 串流，則應使用「一般媒體串流」最佳化類型。

Azure CDN from Verizon 可直接以「一般 Web 傳遞」最佳化類型傳遞串流媒體。
 
## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-media-streaming-in-azure-cdn-from-akamai"></a>設定 CDN 端點以在 Azure CDN from Verizon 中將媒體串流的傳遞最佳化
 
您只要在建立端點期間選取「最佳化」屬性選取之下的「一般媒體串流」或「點播視訊媒體串流」選項，即可設定 CDN 端點，以透過 Azure 入口網站將大型檔案的傳遞最佳化。 若要這樣做，您也可以使用 REST API 或任何用戶端 SDK。 以下螢幕擷取畫面說明透過 Azure 入口網站的程序。 
  
![新增端點](./media/cdn-media-streaming-optimization/01_Adding.png)

*圖 1：從 CDN 設定檔新增 CDN 端點*

![選取的串流](./media/cdn-media-streaming-optimization/02_Creating.png) 
  
*圖 2：以選取的點播視訊媒體串流建立 CDN 端點* 
 
建立 CDN 端點後，就會對所有符合特定準則的檔案套用最佳化。 下一節詳細說明這一點。 
 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Azure CDN from Akamai 的媒體串流最佳化
 
Akamai 提供的媒體串流最佳化適用於使用個別媒體片段進行傳遞的即時或 VOD 串流媒體，而不適用於透過漸進式下載或使用位元組範圍要求傳輸的單一大型資產。 如需媒體傳遞的該樣式，請查看[大型檔案最佳化](cdn-large-file-optimization.md)。

使用一般媒體傳遞或 VOD 媒體傳遞最佳化類型時，將使用 CDN 網路搭配後端最佳化更快速傳遞媒體資產，以及使用以經過一段時間學習的最佳做法為基礎的媒體資產組態。

### <a name="caching"></a>快取

如果 Azure CDN from Akamai 偵測到資產是串流資訊清單或片段 (請參閱下列完整清單)，則 CDN 將使用與一般傳遞不同的快取到期時間。 一如以往，將會接受從原始伺服器傳送的 cache-control 或 expires 標頭，而如果資產不是媒體資產，則會使用一般 Web 傳遞的到期時間進行快取。

當許多使用者要求還不存在的片段時 (例如在無法當下從原始伺服器取得封包的即時資料流中)，短暫的負向快取時間很適合用於原始伺服器卸載。 較長的快取間隔也有助於從原始伺服器卸載要求，因為通常不會修改影片內容。
 

|    | 一般<br> Web<br>偵錯 | 一般<br> 媒體<br> 串流 | VOD<br>媒體<br> 串流  
--- | --- | --- | ---
快取 - 正向 <br> HTTP 200、203、300、 <br> 301、302 和 410 | 7 天 |365 天 | 365 天   
快取 - 負向 <br> HTTP 204、305、404 <br> 和 405 | 無 | 1 秒 | 1 秒
 
### <a name="dealing-with-origin-failure"></a>處理原始伺服器失敗  

一般媒體傳遞和 VOD 媒體傳遞也都有以典型要求模式的最佳做法為基礎的原始伺服器逾時和重試記錄。 例如，由於一般媒體傳遞同時以即時和 VOD 媒體傳遞為目標，所以會因為即時串流講求時效性而使用比較短的連線逾時。

當連線逾時，CDN 會先重試特定次數，再將 504 閘道逾時錯誤傳送給用戶端。 

當檔案符合檔案類型和大小條件清單，CDN 會使用媒體串流行為，不然會使用一般 Web 傳遞。 
   
### <a name="conditions-for-media-streaming-optimization"></a>媒體串流最佳化的條件 

下表列出此最佳化所需滿足的準則集合 
 
支援的串流類型 | 副檔名  
--- | ---  
Apple HLS | m3u8、m3u、m3ub、key、ts、aac
Adobe HDS | f4m、f4x、drmmeta、bootstrap、f4f、<br>Seg-Frag URL 結構 <br> (matching regex: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd、dash、divx、ismv、m4s、m4v、mp4、mp4v、 <br> sidx、webm、mp4a、m4a、isma
Smooth Streaming | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Azure CDN from Verizon 的媒體串流最佳化

Azure CDN from Verizon 能夠直接使用「一般 Web 傳遞」最佳化類型傳遞串流媒體資產。 根據預設，CDN 上也有一些功能可直接協助傳遞媒體資產。

### <a name="partial-cache-sharing"></a>部分快取共用

此功能能夠從 CDN 提供部分快取的內容給新要求。 這表示，如果對 CDN 的第一個要求導致快取遺漏，則會將要求傳送到原始伺服器。 雖然此內容仍會載入 CDN 快取中 (但仍未完成)，但是對 CDN 的其他要求可以開始取得此資料。 

### <a name="cache-fill-wait-time"></a>快取填滿等候時間

搭配部分快取共用，快取填滿等候時間功能會強迫邊緣伺服器保存相同資源的所有後續要求，直到原始伺服器的 HTTP 回應標頭送達為止。 如果原始伺服器的 HTTP 回應標頭在計時器終止前送達，則會在成長中快取之外為所有暫停的要求提供服務 (同時由原始伺服器的資料填滿)。 根據預設，快取填滿等候時間會設定為 3000 毫秒。 


