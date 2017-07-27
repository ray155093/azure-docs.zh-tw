---
title: "透過 Azure CDN 的大型檔案下載最佳化"
description: "深入探討大型檔案下載最佳化"
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
ms.openlocfilehash: 27e202b05f86eeee7071f3fae145caeba3d66827
ms.contentlocale: zh-tw
ms.lasthandoff: 06/28/2017

---
# <a name="large-file-download-optimization-via-azure-cdn"></a>透過 Azure CDN 的大型檔案下載最佳化

透過網際網路傳遞的內容檔案大小由於功能增強、圖形改善和媒體內容更豐富，而呈現穩定成長。 這是由許多因素所驅動，包括寬頻滲透、大型廉價存放裝置、高畫質影片激增、已連接網際網路的裝置 (IoT) 等。為這類大型檔案提供更快速且有效的傳遞機制，對於確保順暢且愉悅的取用者經驗極為重要。 

傳遞大型檔案本身有幾項挑戰。 首先，下載大型檔案的平均時間可能十分顯著，許多應用程式可能無法循序下載所有資料。 在某些情況下，應用程式可能會在檔案的第一部分之前下載最後一部分。 因此，只要求檔案的一小部分，或使用者暫停下載時，可能會導致下載失敗或延遲到 CDN 從原始伺服器擷取整個檔案之後。 

其次，隨著網際網路上的大型檔案激增，使用者常發現使用者與檔案之間的延遲最終會支配輸送量或使用者可以檢視內容的速度。 此外，網路壅塞和容量問題進一步影響輸送量，而這些問題，再加上伺服器與終端使用者之間的距離更大，造成封包發生遺失的額外機會，進而降低品質。 輸送量有限與封包遺失增多所造成的品質降低，可能表現於完成檔案下載的等待時間大幅增加。 

最後，不會完整傳遞許多大型檔案。 使用者可能會中途取消下載，或只觀賞冗長 MP4 影片的前幾分鐘。 因此，許多軟體和媒體傳遞公司只會傳遞終端使用者所要求的檔案部分。 如此一來，系統只會將要求的部分有效率地散發至網際網路的最遠可及範圍，因而降低來自原始伺服器的輸出流量，且原始伺服器的記憶體和 IO 壓力也隨之降低。 

Azure CDN from Akamai 現在提供一項功能，能夠大規模而有效率地將大型檔案提供給全球使用者，並且減少延遲，同時降低原始伺服器的負載。 透過在 Azure CDN 設定檔下使用「標準 Akamai」定價層建立之 Azure CDN 端點上的 [最佳化] 功能，即可取得這項功能。

## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-large-files"></a>設定 CDN 端點以將大型檔案的傳遞最佳化

您只要在建立端點期間選取「最佳化」屬性選取之下的「大型檔案下載」選項，即可設定 CDN 端點，以透過 Azure 入口網站將大型檔案的傳遞最佳化。 若要這樣做，您也可以使用 REST API 或任何用戶端 SDK。 以下螢幕擷取畫面說明透過 Azure 入口網站的程序。

![新的 CDN 端點](./media/cdn-large-file-optimization/01_Adding.png)  
 
*圖 1：從 CDN 設定檔新增 CDN 端點*
 
![已選取 LFO](./media/cdn-large-file-optimization/02_Creating.png)

*圖 2：在已選取大型檔案下載最佳化的情況下建立 CDN 端點*

建立 CDN 端點後，就會對所有符合特定準則的檔案套用大型檔案最佳化。 下一節詳細說明這一點。

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-akamai"></a>透過 Azure CDN from Akamai 將大型檔案的傳遞最佳化

在 Azure CDN from Akamai 中，您可以使用大型檔案最佳化類型功能，開啟可讓大型檔案傳遞更快速且更有回應的網路最佳化和組態。 Akamai 的一般 Web 傳遞只能快取 1.8GB 以下的檔案，而且可以透過通道傳送 (而非快取) 最多 150GB 的檔案，然而大型檔案最佳化功能允許快取多達 150GB 的檔案。

滿足有關原始伺服器運作方式、要求檔案類型及要求檔案大小的特定條件時，大型檔案最佳化功能就會生效。 在我們深入了解上述各項之前，請務必了解最佳化運作方式的高階概觀。 

### <a name="object-chunking"></a>物件區塊化 

Azure CDN from Akamai 會採用一項稱為物件區塊化的技術，以便在有大型檔案的要求時，從原始伺服器擷取檔案的較小片段。 當 CDN 邊緣/POP 伺服器收到來自終端使用者的完整或位元組範圍檔案要求時，它會先查看檔案類型是否屬於此最佳化支援的檔案類型清單，以及它是否符合檔案大小需求。 如果檔案大小大於 10 MB，則 CDN 邊緣伺服器會開始向原始伺服器要求以 2MB 區塊提供此檔案。 一旦區塊送達 CDN 邊緣伺服器，它就會被快取並立即提供給終端使用者，而 CDN 會以平行方式「預先擷取」下一個區塊。此「預先擷取」藉由讓一個區塊保持在使用者的前面以及降低對終端使用者的延遲，確保較快取得內容。 此程序會繼續執行，直到已下載整個檔案 (如果終端使用者要求整個檔案)、直到所有要求的位元組範圍可用 (如果終端使用者要求位元組範圍)，或直到用戶端終止連線為止。 

在 [RFC 7233](https://tools.ietf.org/html/rfc7233) 可找到位元組範圍要求的詳細資料。

CDN 會在收到區塊時進行快取，而不需要在 CDN 快取上快取整個檔案。 檔案或位元組範圍的後續要求將由 CDN 快取提供服務，而且將使用「預先擷取」向從原始伺服器要求區塊，否則會在 CDN 上快取所有區塊。 由此可見，此最佳化會依賴支援位元組範圍要求的原始伺服器。 _如果原始伺服器不支援位元組範圍要求，此最佳化就不會生效。_ 

### <a name="caching"></a>快取
大型檔案會使用與一般傳遞不同的預設快取到期時間。 它會根據 HTTP 回應碼來區分正向快取與負向快取。 如果原始伺服器透過回應中的 Cache-control 或 Expires 標頭，使用到期時間來指定時間，則 CDN 一律會接受該值。 若未指定原始伺服器，而且檔案符合此最佳化類型的檔案類型和檔案大小條件清單，則 CDN 將使用預設值進行大型檔案最佳化。 否則，CDN 會使用預設值進行一般 Web 傳遞。

 
|    | 一般 Web | 大型檔案最佳化 
--- | --- | --- 
快取 - 正向 <br> HTTP 200、203、300、 <br> 301、302 和 410 | 7 天 |1 天  
快取 - 負向 <br> HTTP 204、305、404 <br> 和 405 | 無 | 1 秒 

### <a name="dealing-with-origin-failure"></a>處理原始伺服器失敗

在大型檔案最佳化類型中，原始伺服器讀取逾時長度會從一般 Web 傳遞的 2 秒增加至 2 分鐘來滿足較大的檔案大小，才不會讓連線提前逾時。

至於一般 Web 傳遞，當連線逾時，我們會先重試特定次數，再將 504 閘道逾時錯誤傳送給用戶端。 

### <a name="conditions-for-large-file-optimization"></a>大型檔案最佳化的條件

下表列出大型檔案最佳化所需滿足的準則集合：

條件 | 值 
--- | --- 
支援的檔案類型 | 3g2、3gp、asf、avi、bz2、dmg、exe、f4v、flv、 <br> gz、hdp、iso、jxr、m4v、mkv、mov、mp4、 <br> mpeg、mpg、mts、pkg、qt、rm、swf、tar、 <br> tgz、wdp、webm、webp、wma、wmv、zip  
檔案大小下限 | 10 MB 
檔案大小上限 | 150 GB 
原始伺服器特性 | 必須支援位元組範圍要求 

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>透過 Azure CDN from Verizon 將大型檔案的傳遞最佳化

Azure CDN from Verizon 可以傳遞大型檔案 (檔案大小沒有上限)，且具有各種功能 (預設開啟) 可讓大型檔案的傳遞更快速。

### <a name="complete-cache-fill"></a>完成快取填滿

Azure CDN from Verizon 具有名為「完整快取填滿」的預設功能，以便 CDN 在初始要求遭到放棄或遺失時將檔案提取至快取中。 

對於使用者通常不會完整下載的大型資產 (例如漸進式下載影片) 而言，此功能非常實用。 因此，Azure CDN from Verizon 預設會啟用此功能。 此預設行為是強制 Edge Server 從原始伺服器起始資產的背景擷取。 在那之後，資產將位於 Edge Server 的本機快取中。 一旦完整物件位於快取中，邊緣伺服器可針對快取的物件滿足 CDN 的位元組範圍要求。

預設「完整快取填滿」行為可以透過 Verizon Premium 層中的規則引擎來停用。

### <a name="peer-cache-fill-hotfiling"></a>對等快取填滿 Hotfiling

這是 Azure CDN from Verizon 的預設功能，其複雜的專屬演算法可能會根據頻寬和彙總要求等計量來運用其他邊緣快取伺服器，以滿足大型、非常熱門物件的用戶端要求。 這項功能可防止將大量額外要求傳送至客戶原始伺服器的情況。 

### <a name="conditions-for-large-file-optimization"></a>大型檔案最佳化的條件

Verizon 的最佳化功能預設會開啟，且檔案大小沒有上限。 

## <a name="additional-considerations"></a>其他考量

使用此最佳化類型時，需要考量其他幾個層面。
 
### <a name="azure-cdn-from-akamai"></a>Azure CDN from Akamai

- 區塊化處理會造成對原始伺服器的額外要求，但是從原始伺服器傳遞的整體資料量將會變小許多，因為區塊化導致 CDN 的快取特性更完善。
- 此外，會有一個附加好處：因為傳遞檔案的較小片段，而減少原始伺服器的記憶體和 IO 壓力。 
- 對於在 CDN 快取的區塊而言，直到快取中的內容到期或因為其他原因而從快取中收回，原始伺服器才會有其他要求。 
- 使用者可以向 CDN 提出範圍要求，這些要求會被視為任何一般檔案處理。 只有在要求檔案是有效的檔案類型且位元組範圍介於 10 MB 與 150 GB 之間，才會套用最佳化。如果您平均的要求檔案大小小於 10 MB，則可改用一般 Web 傳遞。

### <a name="azure-cdn-from-verizon"></a>Azure CDN from Verizon

一般 Web 傳遞最佳化能夠傳遞大型檔案。

