---
title: "Microsoft Azure Marketplace 賣方指南 | Microsoft Docs"
description: "本指南針對商務使用者和獨立軟體廠商 (ISV) 的產品經理而設計，他們有興趣向 IT 專業人員和開發人員銷售 Azure 認證的虛擬機器映像。"
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: aa6ae2b7e6ad0c6f91503d026ec8fd6933b6cfbb
ms.lasthandoff: 04/12/2017


---
# <a name="azure-marketplace-sellers-guide"></a>Azure Marketplace 賣方指南

歡迎使用 Microsoft Azure Marketplace 賣方指南。 本指南針對商務使用者和獨立軟體廠商 (ISV) 的產品經理而設計，他們有興趣向 IT 專業人員和開發人員銷售 Azure 認證的虛擬機器映像。 透過世界各地的 Azure 客戶，[Azure Marketplace](https://azuremarketplace.microsoft.com/) 可以為您的產品提供絕佳的觸角和曝光機會。


> [!NOTE]
> 如果您對於將完成的軟體即服務 (SaaS) 產品銷售給商務使用者感興趣，您可以調查選項將它們列在 [AppSource](https://appsource.microsoft.com) 中。

在本指南的結束時，您知道哪裡可以找到下列各項的詳細資訊︰

- 什麼是 Azure Marketplace？
- 如何判斷我的產品是否符合 Azure Marketplace？
- 在 Azure Marketplace 上銷售的好處為何？
- 在 Azure Marketplace 上銷售的必要條件 (技術和非技術性) 有哪些？
- 如何建置 Azure 相容的虛擬硬碟 (VHD)？
- 如何申請並註冊為賣方？
- 如何建立及發佈供應項目？
- 如何上市及尋找可用的資源？
- Azure Marketplace 提供哪些報告和深入解析？
- 哪裡可以取得說明和支援？

現在就開始吧！

## <a name="whats-the-azure-marketplace"></a>什麼是 Azure Marketplace？

Azure Marketplace 一個線上應用程式和服務市集，可讓新興和獨立軟體廠商 (ISV) 為全球的 Azure 客戶提供解決方案。 Azure Marketplace 可讓身為 Azure 發行者的您散佈及販售創新虛擬機器映像給其他想要在 Azure 中快速開發其雲端應用程式和行動解決方案的開發者、ISV、IT 專業人員。 Azure Marketplace 支援各種供應項目，例如具備資料處理、資料儲存體及分析層的端對端資料分析應用程式，或分層式電子商務應用程式 (資料、服務和網際網路)。

雲端客戶在搜尋適當的解決方案來符合其獨特需求時會面臨一些挑戰。 Azure Marketplace 提供一個機制來解決這些挑戰及連結客戶與創新 ISV 解決方案，如下表所說明。

| 客戶需求 | Azure Marketplace 解決方案 |
| --- | --- |
| 要求其他雲端平台功能，以符合商務和技術需求 | 提供 Azure 上互補應用程式和服務的快速成長產品組合 |
| 發現要找到正確的應用程式或服務很具挑戰性 | 讓您一次達成應用程式和服務的探索、搜尋及購買 |
| 需要適用於第三方應用程式和服務的可調整部署機制 | 能夠建立及設定可調整的第三方應用程式和服務部署 |
| 需求新的應用程式和服務與現有的解決方案整合及搭配運作 | 輕鬆地整合第三方應用程式和服務與 Azure 上的現有解決方案 |

Azure Marketplace 讓全球客戶可以享受 Azure 合作夥伴生態系統的品質、多元選擇和優勢。 主要優點包括下列各項︰

- 統一提供來自 Microsoft 和合作夥伴之以 Azure 為基礎的供應項目
- 超過 5,000 個供應項目
- 整合式平台經驗
- 流暢的設定、部署與管理

## <a name="is-the-azure-marketplace-right-for-my-business"></a>Azure Marketplace 適合我的企業嗎？

您現在可能很疑惑 Azure Marketplace 是否適合您的企業，如果適合，您將有何收穫？ Azure Marketplace 可創造新的銷售機會，為客戶供應您在 Azure 上提供的軟體和服務︰

- **在新的客戶市場中銷售擴充的 Azure 解決方案產品組合**︰這是向 Microsoft 線上訂用帳戶方案 (MOSP) 和 Microsoft 企業合約客戶追加銷售及交叉銷售 Marketplace 供應項目與 Azure 服務的機會。 您可以輕易地將 Marketplace 供應項目合併至您的客戶方案和 Azure 案例推銷中。
- **提高商務價值並增加現有和新客戶帳戶的交易規模**：Azure Marketplace 有助於提升交易規模、處理客戶將工作負載移到雲端時遇到的難題，並增加交易獲利。 簡單地說，您可以藉由銷售完整的解決方案來提高商務價值，以及處理重大的 Azure 雲端平台落差，以符合客戶需求。
- **藉由銷售 Marketplace 應用程式和服務來吸引較為廣泛的潛在客戶**：Azure Marketplace 可讓您輕鬆地尋找及留住新客戶。 現今許多企業都需要將工作負載轉換至雲端，並適應瞬息萬變的基礎結構環境。 您可以提供適當的應用程式和服務，幫助他們縮減落差。
- **藉由搭售 Marketplace 供應項目與 Azure 服務來補充及擴充 Azure 功能**：Azure Marketplace 可協助您與客戶進行以案例為基礎的交談，讓您以真實和具體的方式建立關係。 您也可以藉由談論相關的端對端 (E2E) 解決方案來解決特定平台落差和客戶需求。 最後，透過銷售解決方案套件組合，你可以充分運用 Azure 平台生態系統來解決幾乎所有客戶的問題，同時提高您的銷售量。

## <a name="what39s-the-customer-base-for-the-azure-marketplace"></a>Azure Marketplace 的客戶群為何？

Azure Marketplace 的客戶廣泛而多元。 因為 Azure 是所有雲端提供者當中客戶群成長最快速的平台之一，所以您會接觸到各種產業類別 (公營和私人機構) 的 IT 專業人員與開發人員 — 從新創公司以至大型企業。

## <a name="how-does-the-azure-marketplace-work"></a>Azure Marketplace 如何運作？

其實很簡單︰經過過核准之後，您可建立 Azure 認證虛擬機器映像，並將它發佈至 Azure Marketplace，Azure 客戶可以在其中迅速尋找、購買及部署您的產品。 更棒的是，客戶很有信心部署您的解決方案，並了解已為了順利使用 Azure 而設定環境，且基礎結構已準備好在數分鐘內投入運作。

Cloud Partner 入口網站 (CPP) 是在 Azure Marketplace 上建立供應項目的中樞。 虛擬機器映像是預先設定的映像，包含完整安裝的作業系統以及一或多個應用程式。 若要認證您的映像，使其準備好進行發行，您必須符合我們將在本指南下一節中討論的特定必要條件。


## <a name="whats-next"></a>後續步驟

如果您已進行到本指南的這個部分，您可能會對自己說︰&quot;我認為 Azure Marketplace_真的_很適合我的產品，但要如何開始？&quot; 這就是本節所要討論的內容。 下面會提供有關在 Azure Marketplace 上運作的資訊︰獲得 Azure 認證、獲准銷售產品，以及在 CPP 中建立供應項目 (圖 1)。

![在 Azure Marketplace 上銷售的程序](./media/cloud-partner-portal-seller-guide/processforselling.png)

圖 1︰在 Azure Marketplace 上銷售的程序

概括而言，您將符合一組技術性和非技術性必要條件，並且準備您的虛擬機器映像。 然後您會提名您的產品並註冊為賣方。 最後，您會新增行銷內容和提交進行發佈。 讓供應項目在 Azure Marketplace 上線之前，您可以在預覽/預備環境中檢閱供應項目。

第一次針對 Azure Marketplace 建立供應項目時，您應該規劃大約_四週_的時間進行基本上架。 可能的話，請在供應項目推出前_六週_建置完成，以便有更多時間來處理媒體和發佈考量事項。

## <a name="how-do-i-become-azure-certified"></a>如何獲得 Azure 認證？

針對 Azure Marketplace 建立供應項目的第一個步驟是獲得 Azure 認證。 這表示要匯編公司資訊、同意參與原則、下載必要工具，以及建置技術元件 (圖 2)。

![獲得 Azure 認證的需求](./media/cloud-partner-portal-seller-guide/azurecertified.png)

圖 2︰獲得 Azure 認證的需求

### <a name="technical-prerequisites"></a>技術性必要條件

請務必在您的產品推出前，仔細檢閱並符合所有的技術性必要條件。 您需要存取 Windows 或 Linux，以及連結到測試工具的 Azure 相容 VHD。

雖然您可以在雲端或內部部署環境中開發 Azure VHD，但我們建議您使用遠端桌面通訊協定直接在雲端開發 Azure VHD。 不過，最後的手段是可以使用[內部部署基礎結構](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise)來下載 VHD 並進行開發。

如需詳細資訊，請參閱[技術性必要條件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)頁面。

### <a name="non-technical-prerequisites"></a>非技術性必要條件

若要成為 Azure Marketplace 的一部分，您必須符合某些非技術性必要條件。 首先，請檢閱並同意 [Azure Marketplace 參與原則](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)的條款。 請注意，Azure Marketplace 中提供的軟體和服務至少必須符合下列其中一項需求：

- **在 Microsoft Azure 上執行**：軟體或服務的主要功能必須在 Microsoft Azure 上執行。
- **在 Microsoft Azure 上部署**：您必須在清單資訊中描述如何在 Microsoft Azure 上部署該軟體或服務。
- **整合或擴充 Microsoft Azure 服務**：您必須在清單資訊中指出該軟體或服務擴充或整合_哪一項_ Azure 服務，以及該軟體或服務_如何_整合或擴充該 Azure 服務。

如 Azure Marketplace 參與原則中所述，您也必須符合下列商務需求︰

- 您的公司 (或其子公司) 必須位於 Azure Marketplace 支援的銷售來源國家/地區。
- 您的產品授權，必須與 Azure Marketplace 支援的計費模式相容。
- 您必須以合乎商業行為的方式 (包括免費、付費或透過社群支援)，負責為客戶提供技術支援。
- 您必須為您的軟體和任何第三方軟體相依性進行授權。
- 為了使您的供應項目可列於 [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) 和 Azure 管理入口網站中，您提供的內容必須符合標準。

最後，您必須同意遵守[使用條款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隱私權聲明](http://www.microsoft.com/privacystatement/default.aspx)以及[Microsoft Azure 認證方案合約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。 

如需常見問題的清單，請參閱 [Azure Marketplace 常見問題集](https://azure.microsoft.com/marketplace/faq/)。

### <a name="virtual-machine-image-preparation"></a>虛擬機器映像準備

現在，您已經檢閱所有必要條件並完成必要的工作，即可如[虛擬機器映像發佈指南](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)中所詳述，繼續建立您的虛擬機器映像供應項目。

本指南可協助您準備將要部署至 Azure Marketplace 的 VHD，其將成為您的 SKU 基礎。 

> [!NOTE]
> 這個程序會因為您是否提供以 Linux 或 Windows 為基礎的 SKU 而有所不同。

> [!NOTE]
>  這個程序可與[帳戶建立和註冊](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration) (如下說明) 同步執行。

### <a name="azure-certification"></a>Azure 認證

取得 _Azure 認證_狀態表示成功完成上架程序。 此狀態讓客戶有信心其 IT 專業人員和開發人員能從受信任的合作夥伴取得採用 Azure 技術的優良解決方案。 Azure 認證解決方案包括︰

- 全域調查
- 判斷與 Azure 平台的相容性
- 線上映像安全合規性
- 沒有病毒或惡意程式碼
- 支援的計費模式

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>如何提名我的產品並獲得核准？

現在是獲得核准以在 Azure Marketplace 上銷售產品 (圖 3) 的時候。 Microsoft 讓您輕鬆地提名您的產品，完成發佈程序，並註冊成為賣方。

![獲准在 Azure Marketplace 上銷售](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

圖 3：獲准在 Azure Marketplace 上銷售的步驟

要獲得核准的第一個步驟是先[提名](https://createopportunity.azurewebsites.net/)您的產品，然後進行註冊和發佈。 請注意，核准可能需要_多達三個工作天_。

一旦核准，您就會收到下列各項︰

- 電子郵件回條，其中包含可免除 $99 美元開發中心申請費用的優惠碼以及 CPP 中的設定檔。
- Azure 認證狀態的預先技術核准，以及用於建立供應項目及認證 VHD 的選項。 (您的開發中心申請必須獲得核准，您可以建立供應項目。)
- 用於發佈入口網站帳戶詳細資料的指示以及發佈程序的概觀。
- 呼叫 Microsoft 上架小組來引導程序及詢問問題的資格。
- 發佈第二個供應項目的能力。 第二次的供應供項目不需要經過核准。 它們可以直接跳到 CPP，但虛擬機器仍必須透過發佈程序進行認證。
- 要求發行協助的指引。 (問題應導向至 Marketplace 發行者[支援連結](https://support.microsoft.com/getsupport?wf=0&amp;tenant=ClassicCommercial&amp;oaspworkflow=start_1.0.0.0&amp;locale=en-us&amp;supportregion=en-us&amp;pesid=15635&amp;ccsid=636233723471685249)。)

最後，您會[將您的帳戶註冊](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)為 Microsoft 賣方。 核准和調查可能需要_長達兩週_的時間，所以利用這段時間在 CPP 中建立 Azure Marketplace 供應項目。

## <a name="how-do-i-create-my-offer-in-the-cloud-partner-portal"></a>如何在 Cloud Partner 入口網站中建立供應項目？

您現在已準備好建立您的供應項目及驗證您的虛擬機器映像！ 若要這麼做，您將使用 Cloud Partner 入口網站 (圖 4)。 您可以將 CPP 視為[發佈及管理解決方案](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-getting-started)的中樞。

![透過 Cloud Partner 入口網站新增供應項目](./media/cloud-partner-portal-seller-guide/newoffersetup.png)

圖 4︰透過 Cloud Partner 入口網站設定新的供應項目

## <a name="what-about-best-practices"></a>關於最佳做法

為了幫助您成為 Azure Marketplace 上的成功賣方，我們開發了一些不需花費大量時間即可產生重大影響的工具和最佳做法。

## <a name="azure-test-drives"></a>Azure 試用產品

[Azure 試用產品](https://azure.microsoft.com/marketplace/test-drives/)很適合用來向潛在客戶展示您的產品，讓他們在購買前有試用選項。 這有助於增加轉換並產生高素質的潛在客戶。

客戶在提供其連絡資訊後，即可存取您預先建置的試用產品體驗︰在真實世界實作案例中，產品重要功能和優點的實際操作、自學試用版。 如此一來，Azure 試用產品可讓您將展品帶到客戶的眼前。

目前，只能在傳統發佈入口網站上發佈試用產品。 您可以在[這裡](https://github.com/Azure/AzureTestDrive/wiki)瀏覽_如何發佈新的試用產品_，以找到有關如何執行這項操作的文件。

[在此](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)深入了解 Azure 試用產品。

## <a name="go-to-market-checklist"></a>上市檢查清單

深入了解我們的[上市方案](https://partner.microsoft.com/go-to-market/)，有助於擴充貴組織的全球觸角。 您也可以運用[合作夥伴行銷中心](http://smartpartnermarketing.microsoft.com/isv)的資源。

推出之前，我們建議採取一些步驟讓您的 Azure Marketplace 供應項目快速吸引目光。 透用簡短的檢查清單，確認您是否已準備好銷售您的供應項目︰

- 我已藉由張貼部落格文章、傳送電子郵件或發出新聞稿，**宣布在 Azure Marketplace 上提供我的供應項目**。
- **我在自己的網站上促銷我的供應項目**，指引客戶前往 Azure Marketplace 上我的供應項目，他們可以在那裡了解、瀏覽、試用、購買及部署。
- **我已發佈試用產品**，以便客戶短暫體驗在 Azure 上即時執行的供應項目。
- **我已啟用隨選潛在客戶開發**，以便每次客戶按一下來部署我的應用程式時，我會收到其名稱和連絡資訊。
- **我已連絡我的 Microsoft 夥伴經理**(如果我有一個) 來探索其他機會。

## <a name="what-about-reports"></a>關於報告

Azure Marketplace 會提供有關訂單、使用量和客戶的報告，您可透過 Azure Marketplace 的[發行者報告入口網站](https://reports.azure.com) 存取這些報告 (圖 5)。 未經處理資料會以可瀏覽的資料表提供，並可以 CSV 或 XLS 檔案形式下載。

![透過發行者入口網站存取報告](./media/cloud-partner-portal-seller-guide/accessingreports.png)

圖 5︰透過發行者入口網站存取報告

此[影片](https://player.vimeo.com/video/200859918)提供報告功能和優點搶先看，其中包含：

- 報告類型︰首頁上的訂單、使用量及客戶趨勢的摘要快照
- 詳細的訂單、使用量及客戶資料
- 以每月摘要或六個月的趨勢檢視方式顯示訂單和使用量
- 顯示為標準的數個深入解析
- 依據下列各項顯示的使用量/訂單︰

-
  - 市場
  - 通道
  - 帶領潮流的供應項目
  - Marketplace 授權類型

詳細的報告會顯示客戶資訊 — 例如公司名稱、連絡資訊，以及地理位置 (包含郵遞區號) — 因此您可以免費提供給銷售人員。 下列清單包含我們提供的客戶相關特定屬性︰

- Reseller
- 名字
- 姓氏
- 電子郵件
- CompanyName
- TransactionDate
- SubscriptionName
- AzureSubscriptionId
- CloudInstanceName
- Order Count
- CustomerCountryRegion
- CustomerCity
- CustomerCommunicationCulture
- CustomerZipCode

我們也會透過說明文件、詞彙和錄製的示範來提供訓練。 如果您需要協助或支援您的報告，您可以開啟[支援票證](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=636233723471685249)。

