---
title: "透過 Azure CDN 進行動態網站加速"
description: "動態站台加速深入探討"
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
ms.date: 06/29/2017
ms.author: v-semcev
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: e886296fd0a0d49975a36fb86347dec8ad30b176
ms.contentlocale: zh-tw
ms.lasthandoff: 07/11/2017

---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>透過 Azure CDN 進行動態網站加速

隨著社交媒體、電子商務和超高度個人化 web 的爆炸性成長，呈現給使用者的內容百分比正快速成長，從而須以即時方式產生。 使用者期望獲得的快速、可靠且個人化 web 體驗，無關乎瀏覽器、位置、裝置或網路。 不過，與這些體驗高度相關的創新，卻也使得頁面下載速度緩慢，並且讓消費者體驗的品質面臨風險。 

標準 CDN 功能包含快取接近使用者的檔案，從而加速靜態檔案傳遞。 不過，使用動態 Web 應用程式，就無法在邊緣位置中快取該內容，因為伺服器所產生的內容是用以回應使用者的行為。 相較於傳統的邊緣快取，要加速傳遞這類內容更為複雜，並且需要端對端解決方案，在從開始到傳遞的整個資料路徑上微調每個元素。 使用 Azure CDN 動態網站加速 (DSA) 後，能大幅改善具有動態內容的網頁效能。

Akamai 中的 Azure CDN 會在端點建立期間透過 [最佳化] 功能表來提供 DSA 最佳化。 這項功能尚未提供 Verizon 設定檔使用，但會在不久的未來推出。 

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>設定 CDN 端點以加速動態檔案傳遞

您可以在建立端點期間選取「最佳化」屬性選取之下的「動態網站加速」選項，即可設定 CDN 端點，以透過 Azure 入口網站將動態檔案的傳遞最佳化。 若要這樣做，您也可以使用 REST API 或任何用戶端 SDK。 

### <a name="probe-path"></a>探查路徑
探查路徑是動態網站加速特定的功能，在建立時需要有效的探查路徑。 DSA 會使用放在原點的小型探查路徑檔案，將 CDN 的網路路由設定進行最佳化。 您可以下載我們的範例檔案並上傳至您的網站，或是針對探查路徑改為使用原點上大約 10 KB 的現有資產 (若有)。

> [!Note]
> DSA 將會產生額外費用。 如需詳細資訊，請參閱價格頁面。

以下螢幕擷取畫面說明透過 Azure 入口網站的程序。
 
![新增新的 CDN 端點](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

*圖 1：從 CDN 設定檔新增 CDN 端點*
 
![使用 DSA 建立新的 CDN 端點](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

*圖 2：在已選取動態網站加速最佳化的情況下建立 CDN 端點*

建立 CDN 端點後，就會對所有符合特定準則的檔案套用 DSA 最佳化。 下一節詳細說明這一點。

## <a name="dsa-optimization-using-azure-cdn-from-akamai"></a>使用 Akamai 中的 Azure CDN 將 DSA 最佳化

Akamai 中的 Azure CDN 上之動態網站加速會使用下列技術，從 Akamai 加速傳遞動態資產：

-   路由最佳化
-   TCP 最佳化
-   物件預先擷取

### <a name="route-optimization"></a>路由最佳化

路由最佳化很重要，因為網際網路是非常動態的位置，其中的流量和暫時中斷經常會變更網路拓撲。 Border Gateway Protocol (BGP) 是網際網路的路由通訊協定，但透過媒介 Akamai 伺服器將會有更快速的路由。 

路線最佳化會選擇與來源之間的最佳路徑，使站台能持續存取，且可透過最快速且最可靠的路由，將動態內容傳遞給使用者。 Akamai 網路會使用一些技術，透過 Akamai 伺服器中不同的節點來收集即時資料並比較各種路徑，以及透過跨開放式網際網路的預設 BGP 路由，判斷來源與 CDN 邊緣之間的最快路由。 這可避免網際網路的壅塞點以及不必要的長路由。 
 
如此一來，就能更快速且更可靠地將完全動態和交易式內容傳遞給使用者，即使它不可快取也一樣。 

### <a name="tcp-optimizations"></a>TCP 最佳化

傳輸控制通訊協定 (TCP) 是網際網路通訊協定套件的標準，可用來傳遞 IP 網路上應用程式之間的資訊。  根據預設，必須有數個來回要求才能設定 TCP 連線，且必須有數個限制才能避免會導致大規模效率不足的網路壅塞。 Akamai 中的 Azure CDN 會處理這點，方法是最佳化下列 3 個區域： 

 - 排除慢速啟動
 - 利用持續連線
 - 調整 TCP 封包參數

#### <a name="eliminating-slow-start"></a>排除慢速啟動

慢速啟動是 TCP 通訊協定的一部分，會藉由限制透過網路傳送的資料量來防止網路壅塞。 它會從傳送者與接收者之間的小型壅塞視窗大小開始，直到觸達最大值或偵測到封包遺失為止。

Akamai 中的 Azure CDN 從排除慢速啟動 3 個步驟中：
1.  Edge Server 的分散式網路會定期將其使用量、連線健康情況、頻寬和其他參數報告給 Akamai 的網路監視系統。 
2.  會與平台上的所有 Edge Server 共用合併報告。 如此一來，所有的 Edge Server 將會察覺其周圍 Edge Server 的健康情況。 
3.  CDN Edge Server 現在能夠提出某些傳輸參數的假設，例如與其附近的其他 CDN Edge Server 通訊時的最佳視窗大小。 這表示，如果 CDN Edge Server 之間的連線健康情況能夠負荷較高的封包資料傳輸，就可增加初始壅塞視窗的大小。 

#### <a name="leveraging-persistent-connections"></a>利用持續連線

相較於使用者直接連線到您的來源，使用 CDN 時，直接連線到您原始伺服器的唯一機器較少。 Akamai 中的 Azure CDN 也會將使用者要求一起加入集區，能與來源建立較少的連線。

如先前所述，TCP 連線會在交握中提出幾個來回要求以建立新的連線。 持續連線 (也就是「HTTP 保持連線」) 會針對多個 HTTP 要求重複使用現有的 TCP 連線，以省下往返時間並加速傳遞。 

#### <a name="tuning-tcp-packet-parameters"></a>調整 TCP 封包參數
Akamai 中的 Azure CDN 也會調整管理伺服器對伺服器連線的參數，並減少擷取網站中內嵌之內容所需的長途往返量，方法是執行下列動作：
1.  增加初始壅塞視窗，讓多個封包無須等候確認即可通過。
2.  減少初始重新傳輸逾時，以便偵測到遺失，並更快速地重新傳輸。
3.  減少最小和最大的重新傳輸逾，從而降低假設封包在傳輸中遺失之前的等待時間。

### <a name="object-prefetch"></a>物件預先擷取
大多數網站是由參考諸如映像和指令碼等各種其他資源的 HTML 網頁所組成。 一般而言，當用戶端要求網頁時，瀏覽器會先下載及剖析 HTML 物件，然後再進行完全載入網頁所需的其他連結資產之要求。 

預先擷取是 HTML 提供給瀏覽器時，並甚至在瀏覽器提出這些物件要求之前，擷取內嵌在 HTML 網頁中之映像和指令碼的技術。 

將 CDN 提供以 HTML 為基礎的網頁給用戶端瀏覽器時開啟的 [預先擷取] 選項預先擷取時，CDN 就會剖析 HTML 檔案，並針對任何連結的資源提出其他要求，並將它儲存在其快取中。 當用戶端提出連結資產的要求時，CDN Edge Server 就已擁有要求的物件，並可以立即提供而不需要在來源之間往返。 此最佳化有助於可快取和不可快取的內容。

## <a name="adaptive-image-compression"></a>調整映像壓縮

某些裝置 (特別是行動裝置) 時常會遇到較緩慢的網路速度。 在這些情節中，對使用者較有利的方式，是在他們的網頁上更快速地接收較小的映像，而非長時間等待完整解析度的映像。

這項功能會在網路速度較慢時自動監視網路品質，並採用標準 JPEG 壓縮方法，以改善傳遞時間。

調整映像壓縮 | 副檔名  
--- | ---  
JPEG 壓縮 | .jpg、.jpeg、.jpe、.jig、.jgig、.jgi





