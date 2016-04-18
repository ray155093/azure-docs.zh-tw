<properties 
    pageTitle="Azure RemoteApp - 利用一些常見案例測試您的網路頻寬使用量 | Microsoft Azure"
	description="了解常見的使用量案例，可協助您找出適用於 Azure RemoteApp 的網路頻寬需求。"
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/31/2016" 
    ms.author="elizapo" />
    
# Azure RemoteApp - 利用一些常見案例測試您的網路頻寬使用量

如同我們在 [Estimate Azure RemoteApp network bandwidth usage (估計 Azure RemoteApp 網路頻寬使用量)](remoteapp-bandwidth.md) 中所討論，找出 Azure RemoteApp 對您的網路會產生何種影響的最佳方式是執行一些使用量測試。在設定時段期間執行這些測試，以及測量每個案例所需的頻寬。如果您有能力，您也可以測量網路封包遺失和網路抖動，以了解將在您的特定環境中建立的網路模式。

    
評估頻寬使用量時，請記得貴公司內不同的使用者會有不同的使用量。例如，文字讀者和作家所耗用的頻寬會比使用視訊的使用者還少。為了獲得最佳結果，請調查您自己的使用者需求，並建立下列最能符合您公司內使用者的案例組合：請記得[檢閱會影響頻寬使用量和使用者體驗的因素](remoteapp-bandwidthexperience.md) - 這將可協助您識別理想的測試。

先閱讀測試相關資訊、挑選您的組合，然後執行它們。您可以使用下表來協助追蹤效能。

>[AZURE.NOTE] 如果您無法執行自己的網路測試，或者您沒有時間去做，請查看我們的[基本的網路頻寬估計值/建議](remoteapp-bandwidthguidelines.md)。不過，您的級距可能有所不同，因此，如果您「可以」執行自己的測試，就應該這樣做。


## 使用量測試
這其中每一個測試的執行時間各有不同，並且會測試各種耗用網路頻寬的功能/特性。請務必選擇最符合個別公司使用者的測試組合。
 
### 高階主管/複雜的 PowerPoint - 執行 900-1000秒

使用者可以在全螢幕模式中使用 Microsoft Office PowerPoint，呈現 45-50 張高畫質的投影片。投影片應該包含影像、轉換 (含動畫)，以及貴公司典型的色彩漸層背景。使用者應該在每張投影片上至少花費 20 秒的時間。
    
從簡報中的某一張投影片轉換到下一張投影片時，這個案例會產生暴增流量。
    
### 簡單的 PowerPoint - 執行 ~620 秒

使用者可以在全螢幕模式中使用 Microsoft Office PowerPoint，來呈現約含 30 張投影片的簡單 PowerPoint 檔案。投影片的文字量比高階主管/複雜的 PowerPoint 案例更多，並含有更簡單的背景和影像 (黑色圖表)。
    
### Internet Explorer - 執行 ~250 秒

使用者使用 Internet Explorer 瀏覽網站。使用者完整瀏覽及捲動文字、自然影像及一些圖解圖表的組合。網頁會在遠端桌面工作階段主機 (RD 工作階段主機) 伺服器的本機磁碟機上儲存為 .MHT 檔案。使用者會使用 Page Up 鍵、Page Down 鍵、向上鍵和向下鍵來捲動，並針對每個捲動的按鍵/類型使用各種不同的時間間隔：
    
    - Down - 250 keystrokes very 500 ms
    - Page Up - 36 keystrokes every 1000 ms
    - Down - 75 keystrokes every 100 ms
    - Page Down - 20 keystrokes every 500 ms
    - Up - 120 keystrokes every 300 ms
    
### PDF 文件 - 簡單 - 執行 ~610 秒
使用者會使用 Adobe Acrobat Reader，以各種不同方式來讀取和搜尋 PDF 文件。文件應該包含表格、簡單圖形及多種文字字型。文件長度是 35-40 頁。使用者會使用兩種不同速率、向前和向後，利用四種不同的縮放大小 (調整成一頁、符合寬度、100% 及您選擇的另一個項目) 進行完整捲動。縮放可確保文字 (字型) 會以不同的大小來呈現。使用 Page Up 鍵、Page Down 鍵、向上鍵和向下鍵進行向下捲動，並針對每個捲動使用各種不同的時間間隔。

### PDF 文件 - 混合 - 執行 ~320 秒
使用者會使用 Adobe Acrobat Reader，以各種不同方式來讀取和搜尋 PDF 文件。文件包含高品質的影像 (包括相片)、表格、簡單的圖形，以及多種文字字型。使用者會使用兩種不同速率、向前和向後，利用四種不同的縮放大小 (調整成一頁、符合寬度、100% 及您選擇的另一個項目) 進行完整捲動。縮放可確保文字 (字型) 會以不同的大小來呈現。使用 Page Up 鍵、Page Down 鍵、向上鍵和向下鍵進行向下捲動，並針對每個捲動使用各種不同的時間間隔。

### Flash 視訊播放 - 執行 ~180 秒
使用者檢視內嵌於網頁中以 Adobe Flash 編碼的視訊。網頁會儲存在 RD 工作階段主機伺服器的本機硬碟機中。視訊是透過內嵌的播放程式外掛程式，在 Internet Explorer 內播放。

此案例會模擬使用者檢視包含多媒體的豐富內容網頁。大部分的資料應該透過 VOBR 來使用。

### Word 遠端輸入 - 執行 ~1800 秒
使用者透過 RDP 工作階段輸入文件。按鍵動作是透過 RDP 工作階段從用戶端傳送至在遠端工作階段執行的 Microsoft Word 中的文件。輸入速度是每 250 毫秒一個字元 (總共 7050 個字元)。

這是適用於知識工作者的最常見案例之一。此案例會測試使用者輸入最新工作處理器的回應能力。此案例甚至會受到頻寬使用量中的微小變更所影響。

## 追蹤測試結果

您可以使用下表來評估環境中的案例。下面提供的資料僅供說明使用 - 它可能與您所觀察到的情況大不相同。

為了簡單起見，我們假設所有案例的測試條件都一樣：使用相同的 1920 x 1080 像素螢幕解析度，在網路上使用低於 200 毫秒的延遲進行 TCP 傳輸，以及約有 1% 標記為 120 ms+ 的網路抖動。

關於表格：
- **平均體驗**所包含的網路頻寬不會大幅影響使用者產能，且不會排除偶爾發生的視訊或音訊問題。系統仍能利用動態邏輯快速復原。網路頻寬會估計可保證使用者體驗品質的嘗試次數。
 - **值得注意的問題 (中斷點)** 包含使用者可能會在他們的體驗中注意到重大問題的網路頻寬，而其產能會在一段顯著的期間內受到影響。此時，因為沒有足夠的網路頻寬，所以 RDP 演算法更是雪上加霜，且無法保證使用者的高品質體驗。
 - **建議**包含用來取得較佳或極佳體驗所建議的網路頻寬。它通常是一個高於對應**平均體驗**資料行中之值的步驟。
 - **注意事項**包括觀察值和註解。
 
| 測試 | 平均體驗 | 值得注意的問題 (中斷點) | 建議的網路頻寬 | 注意事項 |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| 高階主管/複雜的 PPT | 10 MB/秒 | 1 MB/秒 | > 10 MB/秒，通常使用 100 MB/秒 | 在 1 MB/秒內遺失許多動畫 |
| 簡單的 PPT | 5 MB/秒 | 256 KB/秒 | 10 MB/秒 | 在 256 KB/秒內投影片是以可通知的延遲載入 |
| Internet Explorer | 10 MB/秒 | 1 MB/秒 | > 10 MB/秒，通常使用 100 MB/秒 | 在 1 MB/秒內，Web 視訊是模糊且不穩定的，快速捲動會發生問題 |
| 簡單的 PDF | 1 MB/秒 | 256 KB/秒 | 5 MB/秒 | 在 256 KB/秒內，需要花時間來載入網頁 |
| 混合的 PDF | 1 MB/秒 | 256 KB/秒 | 5 MB/秒 | 在 256 KB/秒內，頁面佔用了大量的時間來載入 |
| Flash 視訊播放 | 10 MB/秒 | 1 MB/秒 | > 10 MB/秒，通常使用 100 MB/秒 | 在 1 MB/秒內，視訊呈現顆粒狀，並捨棄一些畫面格 |
| Word 遠端輸入 | 256 KB/秒 | 128 KB/秒 | 1 MB/秒 | 在 256 KB/秒內，使用者可能會注意到按鍵動作之間的時間 |

若要評估每位使用者的網路頻寬，請建立上述案例的組合以及所需網路頻寬的對應比例。選擇您的案例所需的最高數字。由於使用者幾乎不會單獨使用系統，因此，請考慮針對會同時在同一個網路上工作的使用者進行部分保留。
     
## 詳細資訊
- [Estimate Azure RemoteApp network bandwidth usage (評估 Azure RemoteApp 網路頻寬使用量)](remoteapp-bandwidth.md)

- [Azure RemoteApp - how do network bandwidth and quality of experience work together? (Azure RemoteApp - 如何兼顧網路頻寬和體驗品質？)](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp network bandwidth - general guidelines (if you can't test your own) (Azure RemoteApp 網路頻寬 - 一般指引 (如果您無法自行測試))](remoteapp-bandwidthguidelines.md)

<!---HONumber=AcomDC_0406_2016-->