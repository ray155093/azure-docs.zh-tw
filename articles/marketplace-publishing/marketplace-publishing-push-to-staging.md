<properties
   pageTitle="準備和測試優惠以部署至 Azure Marketplace | Microsoft Azure"
   description="將供應項目部署至 Azure Marketplace 之前，關於提供行銷內容、設定價格方案和測試供應項目的詳細指示。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="04/26/2016"
   ms.author="hascipio"/>

# 使用行銷內容完成供應項目建立程序
在發佈程序的這個步驟中，您需要在 Azure Marketplace 中提供特定的行銷內容，以及關於您的優惠和 (或) SKU 的詳細資料。例如，您將提供產品的描述、公司標誌、價目表、方案的詳細資料，以及其他將您的優惠和 (或) SKU 推送至預備環境的必要資訊。此資訊會做為 Azure 入口網站中的行銷內容。您將在[發佈入口網站][link-pubportal]中開始進行此程序。

## 步驟 1：提供 Marketplace 行銷內容
**英文是預設值，並且是唯一支援的語言。** 請確定欄位中提供的所有資訊都是英文。在您進入預備環境之前，所有資訊皆可隨時編輯。

  1. 移至發佈入口網站 [https://publish.windowsazure.com](https://publish.windowsazure.com)。
  2. 在左側功能表上，按一下 [行銷] 索引標籤。
  3. 在主面板中，按一下 [英文 (美國)] 按鈕。

  > [AZURE.IMPORTANT] 所有欄位都必須填入內容 (包含影像)，才能推送至預備環境。

### 詳細資料
1. 在 [詳細資料] 索引標籤下方，輸入優惠標題 (最多 50 個字元)、優惠摘要 (最多 100 個字元)、優惠完整摘要 (最多 256 個字元)、優惠描述 (最多 1300 個字元)、標誌
2. 在 [方案] 索引標籤下方，輸入 SKU 標題 (最多 50 個字元)、SKU 摘要 (最多 100 個字元)、SKU 描述 (最多 2000 個字元)
3. 請不要在優惠和 SKU 描述下方輸入重複文字。
4. 請不要在 SKU 標題和優惠完整摘要下方輸入重複文字。
5. 請不要在 SKU 標題和優惠摘要下方輸入重複文字。
6. 以 PNG 格式上傳必要規格的影像 (在發佈入口網站中提及)，每種大小各一個。
7. 請確定標誌遵循下面所提及的 Azure Marketplace 標誌指導方針。

  ![繪圖](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Azure Marketplace 標誌指導方針**

發佈入口網站中所上傳的所有標誌都應該遵循下列指導方針︰

- Azure 設計具有簡單的調色盤。請保持標誌上具有最少的主要和次要色彩數目。
- 標誌不應該放在白色背景上。建議使用簡單主要色彩或透明背景。
- 請不要在標誌上使用漸層背景。
- 避免在標誌上放置文字 (甚至是公司或品牌名稱)。
- 您標誌的外觀與風格應該是「一般」，而且應該避免漸層。
- 標誌不應該自動進行縮放。
- 小型標誌的大小應該是 40 X 40 像素
- 中型標誌的大小應該是 90 X 90 像素
- 大型標誌的大小應該是 115 X 115 像素
- 寬標誌的大小應該是 255 X 115 像素
- 主圖標誌的大小應該是 815 X 290 像素

  ![繪圖](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**主圖標誌橫幅的其他指導方針：**

- 主圖標誌為選用項。您可以選擇不上傳主圖標誌。
- 優惠上線之後，會自動將發佈者顯示名稱、SKU 標題、優惠完整摘要和 [建立] 按鈕內嵌在主圖標誌內。因此您在設計主圖標誌時不需要輸入它們。
- 以白色字型色彩顯示發佈者顯示名稱、SKU 標題和優惠完整摘要時，您應該避免將主圖圖示的背景保持為白色或任何淺色色彩。
- 您應該在主圖圖示頂端保留上面文字的空間。文字的空間為 415x100，並從左邊位移 370 個像素。

  ![繪圖](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### 連結
在左側工具列的 [連結] 索引標籤上，輸入任何可能對客戶有幫助的資訊連結。輸入每個連結的名稱和 URL。

![繪圖](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### 範例影像 (選擇性)
> [AZURE.NOTE] 加入範例影像是選擇性步驟。您可以填寫其他行銷內容來符合推送至預備環境的需求。

在左側功能表的 [範例影像] 索引標籤上，按一下 [上傳新的影像] 以上傳新的影像。如果目前已有影像而您想要取代它，請按一下 [取代影像]。

![繪圖](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### 法律
在 [法律聲明] 索引標籤上，提供您的使用原則/條款連結。在大型 [使用條款] 方塊中輸入或貼上條款。法律使用條款的字元限制為 1,000,000 個字元。

![繪圖](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

> [AZURE.NOTE] 對於虛擬機器，一旦 SKU 已預備上線或上線，您就無法變更下列項目︰**優惠識別碼**、**發佈者識別碼**和 **SKU 識別碼**。

## 步驟 2：設定價格
### 定價模式
|定價模式 |說明 |
|---------------|------------------------------------------|
|基本| 購買時支付的每月分期費率，例如 $10 美元/月|
|耗用量 (也稱為使用量、計量) | 每次使用付費，由供應項目發行者定義。無法定義每一基座、每位使用者等等的超額部分，因為並沒有使用者分數的概念，也沒有比例分攤的功能。使用量由合作夥伴每小時報告一次。客戶依每月計費週期付款，而非預付式的月繳方案。 |
|免費試用 | 客戶在一段有限期間內可以免費使用，之後就依正常費率付費。 |
|免費層 | 方案一律免費。 |
| 方案移轉 (也稱為轉換或升級/降級) | 使用者從目前方案改用另一種可接受方案的概念，由合作夥伴定義。 |

**依供應項目類型的價格模式**

> [AZURE.IMPORTANT] 依供應項目類型可用的某些價格模式。請參閱下表。

| | 僅基本 | 僅耗用量 | 基本 + 耗用量 |
|---|---|---|---|
| 虛擬機器映像 | 否 | 是 | 否|
| 開發人員服務 | 是 | 是 | 是 |
| 資料服務 | 是 | 否 | 否 |

### 2\.1.設定 VM 價格
> [AZURE.NOTE] 僅虛擬機器才支援 BYOL。

1.	[定價] 索引標籤下方會出現所有支援的市場。選取適當的市場移至價格欄位。
2.	發佈入口網站上提供的連結將會顯示定價資訊，協助您決定 SKU 的價格。
3.	如果您的 SKU 是 BYOL，請選取外部授權 (BYOL) SKU 可用性的核取方塊。
4.	如果您的 SKU 為每小時，請輸入軟體的價格。沒有價格的 SKU 無法購買或使用。

  > [AZURE.NOTE] 如果您同時有 BYOL 和每小時 SKU，請確定已涵蓋這兩個必要條件：BYOL 核取方塊和每小時的價格值。

5.	定價精靈隨即開啟。請繼續進行此程序以完成您的定價，如果您選擇允許在您指定市場之外購買，請為其他國家定價。
6.	有些國家採用 ISV 匯款。若要在 ISV 匯款國家銷售，您必須能夠在您的 SKU 上索取與收集稅金，而且您應該計算與支付稅金給該國政府。Microsoft 不負責提供法務或稅務指導。如需銷售目標國家/地區的相關資訊，請參閱本文件簡介中的＜優惠的銷售目標國家/地區＞一節。

  > [AZURE.NOTE] 對於虛擬機器，一旦 SKU 上線，您就無法改變下列項目，因為這會影響現有客戶的計費︰**價格變更**、**計費模式變更**和**計費區域移除**。

### 2\.2.設定開發人員服務價格
方案可以是基本 + 耗用量的任意組合，其中基本是每月價格，而超額部分是每次使用付費價格(詳細資訊請見下文)。

**範例：**Contoso 開發人員服務供應項目

| 規劃 | 價格 | 包括 | 移轉路徑 |
|-------|------|-------|-------|
|免費|$0 美元/月|基本功能。|可以移轉至其他方案|
|Bronze|$10 美元/月|基本功能和 1,000 個功能 X 的配額。|可以移轉至 Bronze Plus、Silver 和 Gold 方案|
|Bronze Plus|免費試用期間：$0 美元/月 + $0 美元/meter01 |基本功能和 10,000 個功能 X 的配額。功能 X 配額用完時，客戶可以透過 meter01，依每次使用付費。|可以移轉至 Silver Plus 和 Gold 方案|
|Bronze Plus| 付費期間 (也稱為免費試用到期)：$10 美元/月 + $0.05 美元/meter01|基本功能和 10,000 個功能 X 的配額。功能 X 配額用完時，客戶可以透過 meter01，依每次使用付費。|可以移轉至 Silver Plus 和 Gold 方案|
|Silver|$0.15 美元/meter01|客戶可以透過 meter01 (適用於 X 功能)，依每次使用付費。|可以移轉至 Bronze 和 Gold 方案|
|Silver Plus|$20 美元/月 + $0.15 美元/meter01 + $0.01 美元/meter02|基本功能及 10,000 個功能 X 和 100 個功能 Y 的配額。功能 X 配額用完時，客戶可以透過 meter01，依每次使用付費。功能 Y 配額用完時，客戶可以透過 meter02，依每次使用付費。|可以移轉至 Bronze Plus 和 Gold 方案|
|Gold|$1000 美元/月|10,000 個功能 X、1,000 個功能 Y 和無限制的功能 Z 的配額。|可以移轉至所有方案，免費除外|

## 步驟 3：提供支援資訊
合約詳細資料僅用於合作夥伴與 Microsoft 之間的內部通訊。支援 URL 可供一般客戶使用。

1.	移至發佈入口網站左側的 [支援] 標題。
2.	在 [工程連絡人] 下方輸入資訊。
3.	在 [客戶支援] 下方輸入資訊。如果您只提供電子郵件支援，請輸入虛設的電話號碼，該內容會改用您提供的電子郵件。
4.	輸入支援 URL。

## 步驟 4：選擇 Azure Marketplace 類別
[類別] 索引標籤會提供選取項目的陣列。您的優惠可能會落在這些選取項目中，而且您最多可選取五個類別。

## 行銷活動的呈現方式
以下是如何在 [Azure Marketplace 網站](https://azure.microsoft.com/marketplace/)上和 [Azure 入口網站](https://portal.azure.com)中使用優惠行銷資訊的詳細檢視。

### Azure Marketplace
![繪圖](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![繪圖](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Azure.com Marketplace 網站上的優惠清單*

![繪圖](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Azure.com Marketplace 網站上的優惠描述詳細資料*

![繪圖](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Azure.com Marketplace 網站上的優惠定價詳細資料*

### Azure 入口網站
![繪圖](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Azure 入口網站中的優惠清單*

![繪圖](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Azure 入口網站中的優惠描述詳細資料*

## 後續步驟
既然已載入您的 Marketplace 內容，讓我們繼續在預備環境中測試您的優惠。不過，您必須從下列清單選取適當的優惠類型，因為步驟會隨著優惠類型而有所不同。

||虛擬機器映像 |開發人員服務 | 資料服務 | 解決方案範本 |
|----|----|----|----|----|
| **步驟 3：推送優惠至預備環境** | [在預備環境中測試您的 VM 優惠](marketplace-publishing-vm-image-test-in-staging.md) | 私人預覽 | [在預備環境中測試您的資料服務優惠](marketplace-publishing-data-service-test-in-staging.md) | [在預備環境中測試您的解決方案範本](marketplace-publishing-solution-template-test-in-staging.md) |

## 另請參閱
- [使用者入門：如何將優惠發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0504_2016-->