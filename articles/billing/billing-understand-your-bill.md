---
title: "了解 Azure 帳單 | Microsoft Docs"
description: "了解如何閱讀並了解 Azure 訂用帳戶的使用情形和帳單"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: erihur;genli
translationtype: Human Translation
ms.sourcegitcommit: 29a6290a19186d5bc8a07d87617b47d21529d6b7
ms.openlocfilehash: 2e99332e2a2b023c00669b3ad5ea07f0c791a805
ms.lasthandoff: 02/14/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>了解 Microsoft Azure 帳單
若要了解 Azure 帳單，請檢閱發票和費用摘要，以及各個詳細的每日使用量檔案。 本文說明發票和每日使用量檔案中顯示的大部分詞彙。 若要取得這些檔案，請參閱[如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)。 如果您使用的是免費試用的訂用帳戶，您可以取得每日使用量資訊，但是您沒有發票。

Microsoft Azure 訂用帳戶的費用會因費率方案而異。 某些費率方案 (例如 Visual Studio Enterprise (MPN) 訂閱者) 包含您可以根據需求用於任何 Azure 服務的每月信用額度。

在上一個計費期間結束時，最多可能會有 24 小時的使用量顯示在您目前的帳單中。 此外，針對國際客戶而列在帳單明細上的費用僅為估算用途， 因為銀行換匯的費用不同。

## <a name="a-namepdfa-understand-your-invoice-pdf"></a><a name="pdf"></a> 了解發票 (.pdf)
發票會提供費用的摘要。 您可以從 [Azure 入口網站](https://portal.azure.com)下載可攜式文件格式 (.pdf) 的發票。 如需詳細資訊，請參閱[如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)。 

下列各節列出您在發票上看到的大部分詞彙以及每個詞彙的說明。

### <a name="account-information"></a>帳戶資訊
[帳戶資訊] 區段會顯示您使用量和設定檔的相關資訊。

![頁首](./media/billing-understand-your-bill/Header.png)

| 詞彙 | 說明 |
| --- | --- |
| 發票編號 |可用於追蹤的唯一發票識別碼 |
| 計費週期 |此發票涵蓋的日期範圍 |
| 發票日期 |產生發票的日期 |
| 付款方法 |帳戶 (發票或信用卡) 上所使用的付款類型。 如果您需要更新信用卡的資訊，請參閱[如何變更用於支付 Azure 訂用帳戶的信用卡](billing-how-to-change-credit-card.md)。 如果您代表企業，也可以透過開立發票的付款方法來支付 Azure 訂用帳戶費用，如支票、隔夜支票及電匯。 請參閱 [Azure 帳單 - 如何開立發票](https://azure.microsoft.com/pricing/invoicing/)。 |
| 帳單收件者 |Microsoft Azure 付款位址。 若要了解如何更新帳單資訊，請參閱[如何變更用於支付 Azure 訂用帳戶的信用卡](billing-how-to-change-credit-card.md)。 |
| 訂用帳戶優惠 |已購買之訂用帳戶優惠的類型 (隨用隨付、BizSpark Plus、Azure Pass 等等)。 如果您要將 [隨用隨付] 變更為其他訂用帳戶優惠，請參閱[將 Azure 訂用帳戶切換至其他優惠](billing-how-to-switch-azure-offer.md)。 |
| 帳戶擁有者的電子郵件 |註冊 Microsoft Azure 時所使用的帳戶電子郵件地址。 若要變更電子郵件地址，請參閱[如何變更連絡人電子郵件、地址和電話號碼等 Azure 帳戶的設定檔資訊](billing-how-to-change-azure-account-profile.md)。 |

### <a name="understand-the-invoice-summary"></a>了解發票摘要
帳單的 [發票摘要] 區段會摘要說明上次帳單之後的交易以及目前的使用量費用。

![發票摘要](./media/billing-understand-your-bill/InvoiceSummary.png)

帳單的前期結餘、付款與未付餘額區段會摘要說明上期帳單之後的交易。

| 詞彙 | 說明 |
| --- | --- |
| 前期結餘 |上次帳單算起的應付總金額 |
| 付款 |套用至上次帳單的總付款金額 |
| 未付餘額 (從上一個計費週期) |上次帳單之後套用至您帳戶的任何帳單調整 (信用額度或結餘) |

### <a name="understand-the-current-charges"></a>了解目前費用
帳單的 [目前費用] 區段會顯示有關每月費用的詳細資料。 

| 詞彙 | 說明 |
| --- | --- |
| 使用費用 |使用費用是訂用帳戶的每月總費用。 我們會根據您過去一個月的使用量向您收取費用。 |
| 折扣 |目前帳單適用的服務折扣。 |
| 調整 |目前帳單適用的其他信用額度或未償費用。 例如，如果您享有 Visual Studio Enterprise 含 MSDN 優惠，您會看到每月信用額度。 如果您取消訂用帳戶，您會看到超過您使用訂用帳戶優惠獲得的每月信用額度的所有每月使用費用。 這些費用是從目前計費期間開始日期計算到訂用帳戶取消日期。 |


### <a name="sold-to-and-payment-instructions"></a>買方和付款指示
下表說明您發票上顯示的買方和付款指示。

| 詞彙 | 說明 |
| --- | --- |
| 買方 |這會預先填入帳戶的設定檔地址。 如果您需要變更這個地址，請參閱[如何變更連絡人電子郵件、地址和電話號碼等 Azure 帳戶的設定檔資訊](billing-how-to-change-azure-account-profile.md)。 |
| 付款指示 |如果您的付款方法是發票，這就是指示寄送支票、電匯或隔天送達支票的位置。 如需詳細資訊，請參閱 [Azure 帳單 - 如何開立發票](https://azure.microsoft.com/pricing/invoicing/) |

## <a name="a-namecsva-understand-detailed-usage-charges-csv"></a><a name="csv"></a> 了解詳細的使用量費用 (.csv)
使用量檔案會顯示在目前的計費期間內每個資源的使用量。 此檔案以逗號分隔值 (.csv) 檔案格式提供，您可以使用試算表應用程式開啟這個檔案。 如果您看到兩個可用版本，請下載第 2 個版本。 這是最新的檔案格式。 如需詳細資訊，請參閱[如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)。

使用量費用是訂用帳戶的**每月**總費用減去任何信用額度或折扣。 我們會根據您過去一個月的使用量向您收取費用。  

下列各節說明第 2 版詳細使用量檔案中所示的大部分詞彙。

### <a name="statement"></a>陳述式 
檔案的上方區段會顯示您在上個月帳單週期期間使用的服務。 下表列出本節中所顯示的詞彙和說明。

| 詞彙 | 說明 |
| --- | --- |
|計費期間 |使用資源或服務時的計費期間。 |
|計量類別 |識別這個使用所屬的最上層服務。 |
|計量子類別 |定義 Azure 服務的類型，而且可能會影響費率。 |
|計量名稱 |識別耗用資源的度量單位。 |
|計量區域 |針對根據資料中心位置定價的某些服務，識別資料中心的位置。 |
|SKU |識別每個 Azure 資源的唯一系統識別碼。 |
|單位 |識別服務的計費單位。 例如，GB、小時、10,000 秒。 |
|已耗用的數量 |計費期間所使用之資源的數量。 |
|已包括的數量 |目前計費期間免費提供之支援的數量。 |
|超額數量 |顯示「已耗用的數量」和「已包括的數量」之間的差額。 我們會針對此數量向您收費。 對於不提供優惠所含數量的隨收隨付優惠，這個總數會與「已耗用的數量」相同。 |
|承諾用量期間 |顯示從您的 6 或 12 個月優惠相關承諾用量中減去的資源費用。 資源費用得依時間順序減去。 |
|貨幣 |目前計費期間所使用的貨幣。 |
|超額部分 |顯示超過您的 6 或 12 個月優惠相關承諾用量的資源費用。 |
|承諾用量費率 |顯示根據您的 6 或 12 個月優惠相關總承諾用量的承諾用量費率。 |
|費率 |每個計費單位向您收取的費率。 |
|值 |顯示 [超額數量] 資料行和 [費率] 資料行兩者相乘的結果。 如果「已耗用的數量」沒有超過「已包括的數量」，則此資料行中沒有任何費用。 |

### <a name="daily-usage"></a>每日使用量 

檔案的 [每日使用量] 區段會顯示影響收費率的使用量詳細資料。 下表列出本節中所顯示的詞彙和說明。 

| 詞彙| 說明 |
| --- | --- |
|使用日期 |使用資源的日期。 |
|計量類別 |識別這個使用所屬的最上層服務。 |
|計量識別碼 |計費的計量識別碼。 這是用於價格計費使用量的識別碼。 |
|計量子類別 |定義可能會影響費率的 Azure 服務類型。 |
|計量名稱 |識別耗用資源的度量單位。 |
|計量區域|針對根據資料中心位置定價的某些服務，識別資料中心的位置。 |
|單位 |識別服務的計費單位。 例如，GB、小時、10,000 秒。 |
|已耗用的數量 |當日已耗用的資源量。 |
|資源位置 |識別正在執行資源的資料中心。 |
|已耗用的服務 |您所使用的 Azure 平台服務。 |
|資源群組 |部署的資源正在其中執行的資源群組。 如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。 |
|執行個體識別碼 |資源的識別碼。 識別碼包含在建立時為資源指定的名稱。 這會是資源的名稱或完整的資源 ID。 如需詳細資訊，請參閱 [Azure Resource Manager API](/rest/api/resources/resources)。 |
|標記 |您指派給資源的標記。 使用標記為帳單記錄分組。 例如，您可以使用標記，根據使用資源的部門散發成本。 支援發出標記的服務為虛擬機器、儲存體，以及使用 [Azure Resource Manager API](/rest/api/resources/resources) 佈建的網路服務。 如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。 |
|其他資訊 |服務專屬的中繼資料。 例如，虛擬機器的影像類型。 |
|服務資訊 1 |服務在訂用帳戶上所屬的專案名稱。 |
|服務資訊 2 |這是舊版欄位，可擷取選擇性服務的特定中繼資料。 |

## <a name="tips-for-cost-management"></a>成本管理的秘訣
您最好主動控制您的成本︰

- 使用[價格計算機](https://azure.microsoft.com/pricing/calculator/)、[擁有權總成本計算機](https://aka.ms/azure-tco-calculator)，以及新增服務的時間預估成本
- [設定帳務警示](billing-set-up-alerts.md)
- [定期在 Azure 入口網站檢閱您的使用量和成本](billing-getting-started.md#costs)

若要深入了解，請參閱[開始使用 Azure 計費與成本管理](billing-getting-started.md)。

## <a name="how-do-i-make-a-payment"></a>我要如何進行付款？
如果您設定信用卡或轉帳卡做為付款方法，則會自動扣款。 如果您設定為使用 [發票付款方法](https://azure.microsoft.com/pricing/invoicing/)，請將您的付款交付至您發票底部所列的位置。 如需詳細說明，請 [連絡支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>如何檢查信用卡付款的狀態？
[建立支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以要求查看付款的狀態。 

## <a name="what-about-marketplace-orders-or-external-service-charges"></a>那 Marketplace 訂單或外部服務費用呢？
外部服務先前稱為 Marketplace 訂單。 外部服務是由獨立的服務廠商所提供，不過已整合在 Azure 生態系統中。 若要深入了解，請參閱[了解您的 Azure 外部服務費用](billing-understand-your-azure-marketplace-charges.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。 
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
 




