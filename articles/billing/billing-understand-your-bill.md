---
title: "了解您的帳單 | Microsoft Docs"
description: "了解如何閱讀並了解 Azure 訂用帳戶的使用情形和帳單"
services: 
documentationcenter: 
author: genlin
manager: stevenpo
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2016
ms.author: erihur;genli
translationtype: Human Translation
ms.sourcegitcommit: 25dc3835983f1f51047bc5680138dcc6b3879d7a
ms.openlocfilehash: 5abb8627a2b0d06d9aa948ac35cfd1ba2b08ee31


---
# <a name="understand-your-bill-for-microsoft-azure"></a>了解 Microsoft Azure 帳單
> [!NOTE]
> 如果您對於本文任何內容有需要進一步的協助，請 [連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 以快速解決您的問題。
> 
> 

Microsoft Azure 訂用帳戶的費用會因費率方案而異。 某些費率方案 (例如 Visual Studio Enterprise (MPN) 訂閱者) 包含您可以根據需求用於任何 Azure 服務的每月信用額度。

請注意，先前計費週期中最多 24 小時的潛在使用量可能會列於目前計費週期內。

如需耗用量和費率方案的詳細資訊，請參閱 [Microsoft Azure 購買選項頁面](https://azure.microsoft.com/pricing/purchase-options/)。

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### <a name="view-or-download-a-bill-for-microsoft-azure"></a>檢視或下載 Microsoft Azure 帳單
1. 使用您的 Microsoft 帳戶或組織識別碼登入 [帳戶中心](https://account.windowsazure.com/subscriptions) 。
2. 按一下您想要查看詳細資料和使用狀況的訂用帳戶。
3. 按一下 [帳單記錄] 
   
    ![摘要 - 帳單記錄 - 1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)
4. [帳單記錄]  區段會列出先前計費週期再加上目前未開立帳單期間的對帳單。 目前期間的對帳單是產生估計時的估計費用。 此資訊每天只會更新一次，而且可能不包含截至本日為止的所有使用量。 您的每月帳單可能會與這項估計有所不同。  
   
    ![摘要 - 帳單記錄 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)
5. 按一下 [ **檢視目前對帳單** ] 以檢視產生估計時的估計費用。 此資訊每天只會更新一次，而且可能不包含截至本日為止的所有使用量。 您的每月帳單可能會與這項估計有所不同。
   
    ![摘要 - 帳單記錄 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)
   
    ![摘要 - 帳單記錄 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)
6. 按一下 [ **下載發票** ] 以檢視先前帳單的副本。
   
    ![摘要 - 帳單記錄 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)

> [!NOTE]
> 針對國際客戶所列在帳單上的費用僅為估算用途，因為銀行換匯的費用不同。
> 
> 

以下是在 Microsoft Azure 上可使用之兩個不同優惠的某些範例陳述。

| 優惠類型 | 說明 | 下載 |
|:--- |:--- |:--- |
| Pay-As-You-Go |每個月支付 |[範例檔案](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf) |
| 承諾優惠 |從預付承諾中減少花費 |[範例檔案](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf) |

## <a name="account-information"></a>帳戶資訊
帳戶資訊區段可識別使用量和設定檔的相關資訊。

![頁首](./media/billing-understand-your-bill/Header.png)

| 詞彙 | 說明 |
| --- | --- |
| 發票編號 |可用於追蹤的唯一發票識別碼 |
| 計費週期 |發生使用量的時間範圍 |
| 發票日期 |產生發票的日期 |
| 付款方法 |帳戶 (發票或信用卡) 上所使用的付款類型 |
| 帳單收件者 |Microsoft Azure 付款地址 |
| 訂用帳戶優惠 |已購買之訂用帳戶優惠類型 (隨收隨付、BizSpark Plus、Azure Pass 等等) |
| 帳戶擁有者的電子郵件 |註冊 Microsoft Azure 時所使用的帳戶電子郵件地址 |

## <a name="understand-the-invoice-summary"></a>了解發票摘要
帳單的 [發票摘要]  區段會摘要說明上次帳單之後的交易以及目前的使用費用。

![發票摘要](./media/billing-understand-your-bill/InvoiceSummary.png)

帳單的前期結餘、付款與未付餘額區段會摘要說明上期帳單之後的交易。

| 詞彙 | 說明 |
| --- | --- |
| 前期結餘 |上次帳單算起的應付總金額 |
| 付款 |套用至上次帳單的總付款金額 |
| 未付餘額 (從上一個計費週期) |上次帳單之後套用至您帳戶的任何帳單調整 (信用額度或結餘) |

## <a name="understand-the-current-charges"></a>了解目前費用
帳單的 [目前費用] 區段包含有關每月費用的詳細資料。 連結分為下列各節。

| 詞彙 | 說明 |
| --- | --- |
| 使用費用 |使用費用是訂用帳戶的每月總費用。 我們會根據您過去一個月的使用量事後向您收取費用。 |
| 折扣 |套用至您目前帳單的服務折扣會反映在這個行項目中。 |
| 調整 |其他調整是套用至您目前帳單的其他信用額度或未付費用。 例如，如果您享有 Visual Studio Enterprise 含 MSDN 優惠，您會在這一行項目中看到每月信用額度。 如果您取消訂用帳戶，您會看到超過您優惠所含每月信用額度 (從您目前計費期開始至訂用帳戶取消日期為止) 的每月使用費用。 |

## <a name="footer-information"></a>頁尾資訊
![頁尾](./media/billing-understand-your-bill/footerinformation.png)

## <a name="understand-the-additional-information"></a>了解其他資訊
[其他資訊] 頁面提供可了解發票的其他資源參考，以及可檢視您使用量的連結，以及帳單的其他相關資訊。

![其他資訊](./media/billing-understand-your-bill/AdditionalInformation.png)

### <a name="detailed-usage"></a>詳細的使用量
在 [詳細的使用量] 描述中的連結會將您引導至帳戶中心，您可以在此檢視此訂用帳戶的詳細使用量。  現在有兩個版本可供下載：**.csv 第 1 版**包含舊的命名慣例和使用情況欄位，**.csv 第 2 版**包含每個類別的客戶易記名稱，加上可協助您了解正在 Microsoft Azure 上使用哪些服務的其他欄位。 請注意，在 .csv 第 1 版沒有 Azure Resource Manager 的詳細資訊。 .csv 第 2 版有 Azure Resource Manager 的資訊。

### <a name="additional-information-and-useful-resources"></a>其他資訊和有用資源
此區段包含關於計算執行個體大小、SQL DB 費用等簡單問題的連結，以及可幫助您進一步回答問題的有用連結。

| 詞彙 | 說明 |
| --- | --- |
| 買方 |這會預先填入帳戶的設定檔地址 |
| 付款指示 |本章節包含當您的付款方法是發票時，有關寄送支票、電匯或隔天送達支票等付款指示 |

## <a name="understand-detailed-usage-charges"></a>了解詳細的使用費用
我們持續致力於協助客戶輕鬆管理其 Azure 使用情況，已增強可報告 Azure 服務使用情況和成本的下載使用情況檔案。  下載連結包含兩個版本的使用情況檔案︰

* **第 1 版** 使用預先存在的格式
* **第 2 版** 包含其他資訊和 [每日使用情況] 區段中已更新的資料行名稱。  

使用費用是訂用帳戶的 **每月** 總費用扣除任何信用額度或折扣。 我們會根據您過去一個月的使用量事後向您收取費用。  檔案的上方區段會顯示您在上個月的計費週期中需支付的服務詳細資料。  下表列出每個 .csv 版本檔案的資料行名稱。

| 第 1 版 | 第 2 版 | 說明 |
|:--- |:--- | --- |
| 計費期間 |計費期間 |使用資源時的計費期間。 |
| Name |計量類別 |識別這個使用所屬的最上層服務。 |
| 類型 |計量子類別 |Azure 服務可能會在此資料行中透過類型進一步定義，這可能會影響費率。 |
| 資源 |計量名稱 |識別耗用資源的度量單位。 |
| 區域 |計量區域 |針對根據資料中心位置定價的某些服務，識別資料中心的位置。 |
| SKU |SKU |識別每個 Azure 資源的唯一系統識別碼。 |
| 單位 |單位 |識別服務的計費單位。 例如，GB、小時、10,000 秒。 |
| 已耗用 |已耗用的數量 |包含計費期間所耗用的資源量。 |
| 已包括 |已包括的數量 |包含目前計費期間免費提供的資源量。 |
| 可計費 |超額數量 |如果已耗用數量超過所提供的數量，則此資料行會顯示差異。 我們會針對此數量向您收費。 對於不提供優惠所含數量的隨收隨付優惠，這個總數會與已耗用的數量相同。 |
| 承諾用量期間 |承諾用量期間 |包含從您的 6 或 12 個月優惠相關承諾用量中遞減的資源費用。 您的資源費用會依時間先後順序從承諾用量中進行遞減。 |
| 貨幣 |貨幣 |識別會反映在您目前計費期間的貨幣。 |
| 超額部分 |超額部分 |包含超過您的 6 或 12 個月優惠相關承諾用量的資源費用。 |
| 承諾用量費率 |承諾用量費率 |包含根據您的 6 或 12 個月優惠相關總承諾用量的承諾用量費率。 |
| 費率 |費率 |費率會顯示根據每個可計費單位向您收費的費率。 |
| 值 |值 |顯示將可計費資料行乘以費率資料行的結果。 如果已耗用數量未超過所提供的數量，則此資料行中將不會有任何費用。 |

## <a name="analyze-daily-usage-data"></a>分析每日使用狀況資料
視您的使用量而定，每日使用量資料可能會有數以千計的資料行。 如果您想要分析這項資料，請按一下 [ **下載使用量** ] 並選擇以逗號分格的變數檔案 (.csv) 版本，以便查看適當計費時段的每日使用情況資料。  您可以為下方各版本下載範例 .csv 檔案以供參考。

| Name | 下載 |
|:---:|:---:|
|   詳細的使用情況 .csv 第 1 版 |[範例檔案](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx) |
|   詳細的使用情況 .csv 第 2 版 |[範例檔案](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx) |

![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)

在 .csv 檔案中，會細分項目以顯示目前計費時段中每個資源的耗用量清單。

![csv 快照集](./media/billing-understand-your-bill/csvsnapshotportal.png)

下列資料行會顯示在開始計費期間時影響費率的詳細資料：

| 第 1 版 | 第 2 版 | 說明 |
|:--- |:--- | --- |
| 使用日期 |使用日期 |發出資源的日期。 |
| Name |計量類別 |識別這個使用所屬的最上層服務。 |
| 資源 GUID |計量識別碼 |計費的計量識別碼。  這可當做識別碼，用於價格計費的使用情況。 |
| 類型 |計量子類別 |Azure 服務可能會在此資料行中透過類型進一步定義，這可能會影響費率。 |
| 資源 |計量名稱 |識別耗用資源的度量單位。 |
| 區域 |計量區域 |針對根據資料中心位置定價的某些服務，識別資料中心的位置。 |
| 單位 |單位 |識別服務的計費單位。 例如，GB、小時、10,000 秒。 |
| 已耗用 |已耗用的數量 |包含當日已耗用的資源量。 |
| 子區域 |資源位置 |識別正在執行資源的資料中心。 |
| 服務 |已耗用的服務 |您可以利用這個資料行來追蹤可能無法在名稱資料行中特別識別的個別 Azure 平台服務。 此服務資料行會指出與使用有關的特定服務。 |
| N/A |資源群組 |新增資料行。 部署的資源正在其中執行的資源群組。 請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md) |
| 元件 |執行個體識別碼 |執行中資源的識別碼。 識別碼包含在建立時為資源指定的名稱。 |
| N/A |標記 |新增資料行。 Azure 中的新資源類型可讓您標記資源。 請參閱 [使用標籤來組織 Azure 資源](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) |
| 其他資訊 |其他資訊 |關於服務的其他中繼資料。 |
| 服務資訊 1 |服務資訊 1 |此資料行會提供服務在訂用帳戶上所屬的專案名稱。 |
| 服務資訊 2 |服務資訊 2 |這是舊版欄位，可擷取選擇性服務的特定中繼資料。 |

除了一些新欄位和 csv 第 2 版的名稱變更之外，下列欄位中還有標準化的資料格式化：

* **執行個體識別碼**：執行個體識別碼欄位表示使用者已指定佈建服務的識別碼。 目前，有兩種格式可呈現執行個體識別碼：就是資源的名稱或完整的資源識別碼。 Microsoft Azure 服務正將執行個體識別碼轉換為以標準化完整資源識別碼格式的方式呈現 (/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)。 當服務轉換成新的格式，您會看到執行個體識別碼資料欄位從只有資源名稱變更為資源識別碼。 資源識別碼是 [Azure Resource Manager API](https://msdn.microsoft.com/library/azure/dn790567.aspx) 用來在訂用帳戶中識別資源的格式。

![instanceid](./media/billing-understand-your-bill/instanceid.png)

* **其他資訊**：使用情況 .csv 中的 Additional Info 資料行會指定服務特定的中繼資料。 例如，VM 的影像類型。 目前，服務會在多個資料行中發出服務特定的中繼資料：Additional Info、Service Info1 和 Service Info 2 等欄位。 Microsoft Azure 服務只會標準化 Additional Info 資料行中的發出服務特定中繼資料。  請參閱下方的標準化格式快照集：

![additionalinfo_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

* **標籤**：此資料行包含使用者指定的資源標籤。 標籤可用來分組計費記錄。 例如，您可以使用標籤，根據使用服務的部門散發成本。 深入了解[使用標籤來組織 Azure 資源](../resource-group-using-tags.md)。 支援發出標籤的服務如下：  
  
  * 虛擬機器
  * 儲存體和
  * 使用 [Azure Resource Manager API](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![標籤](./media/billing-understand-your-bill/tags.png)

## <a name="next-steps"></a>後續步驟
* [設定帳務警示](../billing-set-up-alerts.md)
* [管理您的付款方式](../billing-how-to-change-credit-card.md)
* [了解您的 Azure Marketplace 費用](../billing-understand-your-azure-marketplace-charges.md)
* [Azure 計費及訂用帳戶常見問題集](../billing-subscription-faq.md)

> [!NOTE]
> 如果您仍有其他問題，請 [連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 以快速解決您的問題。
> 
> 

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->



<!--HONumber=Nov16_HO4-->


