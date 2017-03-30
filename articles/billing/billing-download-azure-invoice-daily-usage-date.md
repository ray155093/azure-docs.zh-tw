---
title: "下載 Azure 帳單發票和每日使用量資料 | Microsoft Docs"
description: "說明如何下載或檢視您的 Azure 帳單發票和每日使用量資料。"
keywords: "帳單發票,發票下載,azure 發票,azure 使用量"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c920627e11e801bde88b47fc7c7e128e06f59ea8
ms.lasthandoff: 03/22/2017


---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>下載或檢視您的 Azure 帳單發票和每日使用量資料
您可以從 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下載您的發票，或透過電子郵件傳送它。 若要下載您的每日使用量，請移至 [Azure 帳戶中心](https://account.windowsazure.com)。 只有帳戶管理員有權取得帳單發票和使用量資訊。 若要找出誰是訂用帳戶的帳戶管理員，請參閱[轉送 Azure 訂用帳戶的擁有權 - 常見問題集](billing-subscription-transfer.md#faq)。

## <a name="get-your-invoice-in-email-pdf"></a>透過電子郵件取得發票 (.pdf)
您可以選擇加入並設定其他收件者來透過電子郵件接收您的 Azure 發票。 這項功能可能無法用於支援優惠、Enterprise 合約或 Azure in Open 等特定訂用帳戶。

1. 在 [[訂用帳戶] 刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中，選取您的訂用帳戶。 針對您擁有的每個訂用帳戶選擇加入。 依序按一下 [發票] 和 [以電子郵件寄送我的發票]。 

    ![顯示選擇加入流程的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. 按一下 [選擇加入] 並接受條款︰

    ![顯示選擇加入流程的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. 接受合約後，您就可以設定其他收件者︰

    ![顯示選擇加入流程的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
如果在依照這些步驟執行之後並未收到電子郵件，請在[設定檔的通訊喜好設定](https://account.windowsazure.com/profile)中確定您的電子郵件地址是正確的。


## <a name="download-invoice-from-azure-portal-pdf"></a>從 Azure 入口網站下載發票 (.pdf)

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在 [中樞] 功能表中，選取 [訂用帳戶]。 

    ![顯示 [訂用帳戶] 選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. 在 [訂用帳戶] 刀鋒視窗中，選取您想要檢視的訂用帳戶，然後選取 [帳單及使用量]。 

    ![顯示 [帳單和使用量] 選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. 在 [帳單及使用量] 刀鋒視窗中，按一下 [下載發票] 以檢視您的 pdf 發票副本。 

    ![顯示計費期間、下載發票和每個計費期間總費用的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. 按一下計費期間，即可檢視每日使用量。 

如需發票的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。 如需管理成本上的協助，請參閱[使用 Azure 計費與成本管理避免非預期的成本](billing-getting-started.md)。

## <a name="download-usage-from-the-account-center-csv"></a>從帳戶中心下載使用量 (.csv)
1. 以帳戶管理員身分登入 [Azure 帳戶中心](https://account.windowsazure.com/subscriptions)。
2. 選取您需要發票和使用資訊的訂用帳戶。
3. 選取 [帳單記錄]。 

    ![顯示帳單記錄選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 您可以查看過去六個計費期間和目前未收帳期間的對帳單。 

    ![顯示計費期間、下載發票和每日使用量的選項，以及每個計費期間總費用的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 選取 [檢視目前的對帳單]，以查看產生估計時的估計費用。 此資訊每天只會更新一次，可能不會包含您的所有使用量。 您的每月發票可能會與這項估計有所不同。

    ![顯示 [檢視目前對帳單] 選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![顯示目前費用預估的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. 選取 [下載使用量]，以 CSV 檔案形式下載每日使用量資料。 如果您看到兩個可用版本，請下載第 2 個版本。

    ![顯示 [下載使用量] 選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

如需每日使用量的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。 如需管理成本上的協助，請參閱[使用 Azure 計費與成本管理避免非預期的成本](billing-getting-started.md)。

## <a name="noinvoice"></a>為什麼我沒有看到上期的發票？
您沒有看到發票的可能原因如下︰
- 您的訂用帳戶有尚未超過的每月信用額度，或您有免費試用版。 只有在您積欠費用時，才會產生發票。
- 您的 Azure 訂閱不滿 30 天。
- 尚未產生發票。 等到計費期間結束。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。


