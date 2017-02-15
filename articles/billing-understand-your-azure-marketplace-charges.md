---
title: "了解您的 Azure 外部服務費用 | Microsoft Docs"
description: "了解 Azure 中外部服務 (先前稱為 Marketplace) 費用的計費方式。"
services: 
documentationcenter: 
author: adpick
manager: ruchic
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: adpick
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 45e0d553179b63de2d0664314271472f1151d73f


---
# <a name="understand-your-azure-external-service-charges"></a>了解您的 Azure 外部服務費用
本文說明 Azure 中外部服務的計費方式。 外部服務先前稱為 Marketplace 訂單。 外部服務是由獨立的服務廠商所提供，不過已完全整合在 Azure 生態系統中。 了解如何：

* 識別外部服務
* 了解計費方式與其他 Azure 資源的相異之處
* 檢視及追蹤使用外部服務所累積的任何成本
* 管理外部服務訂單和支付方式

## <a name="what-are-azure-external-services"></a>何謂 Azure 外部服務？
外部服務先前稱為 Azure Marketplace。 一般而言，它們是由第三方發佈且適用於 Azure 的服務。 例如，ClearDB 和 SendGrid 是可在 Azure 中購買的外部服務，然而它們並非由 Microsoft 所發行。

### <a name="identify-external-services"></a>識別外部服務
當您佈建新的外部服務或資源時，系統會顯示警告︰

![Marketplace 購買警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> 外部服務乃是由 Microsoft 之外的公司所發佈，不過 Microsoft 產品有時候也會分類為外部服務。
> 
> 

### <a name="external-services-are-billed-separately"></a>外部服務會分開計費
系統會將外部服務視為 Azure 訂用帳戶內的個別訂單。 當您購買服務時，會設定每項服務的計費期間。 這樣做可避免與購買服務之訂用帳戶的計費期間混淆。 您還會收到獨立的帳單，而信用卡收費也會分開進行。

### <a name="each-external-service-has-a-different-billing-model"></a>每項外部服務都有各自的計費模式
有些服務採用隨用隨付的方式計費，有些則使用按月付款模式。 您需要使用信用卡來支付 Azure 外部服務的費用，不能以發票付款的方式來購買。

### <a name="you-cant-use-monthly-free-credits-for-external-services"></a>外部服務不適用於每個月的免費信用額度
如果您使用含有[免費信用額度](https://azure.microsoft.com/pricing/spending-limits/)的 Azure 訂用帳戶，該信用額度將無法套用至外部服務帳單。 請使用信用卡來購買外部服務。

## <a name="view-external-service-spending-and-history"></a>檢視外部服務消費和歷程記錄
在 [Azure 入口網站](https://portal.azure.com/)內，您可以檢視每個訂用帳戶上的外部服務清單： 

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表中，選取 [訂用帳戶]。
   
    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. 在 [訂用帳戶] 刀鋒視窗中，選取您想要檢視的訂用帳戶，然後選取 [外部服務]。
   
    ![在計費刀鋒視窗中選取訂用帳戶](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. 您應該會看到每項外部服務的訂單、發行者名稱、購買的服務層、資源的命名及目前的訂單狀態。 選取外部服務以查看過去的帳單。
   
    ![選取外部服務](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. 在這裡，您可以檢視過去的帳單金額，包括稅額細目。
   
    ![檢視外部服務帳單記錄](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="manage-payment-methods-for-external-service-orders"></a>管理外部服務訂單的付款方法
從[帳戶中心](https://account.windowsazure.com/)更新外部服務訂單的付款方法。

> [!NOTE]
> 如果您使用公司或學校帳戶來購買訂用帳戶，應[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)來變更付款方法。
> 
> 

1. 登入[帳戶中心](https://account.windowsazure.com/)並[瀏覽至 [Marketplace] 索引標籤](https://account.windowsazure.com/Store)
   
    ![在帳戶中心內選取 Marketplace](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. 選取要管理的外部服務
   
    ![選取要管理的外部服務](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. 在頁面右側，按一下 [變更付款方式]。 此連結將帶您前往其他入口網站，以便您管理付款方法。
   
    ![訂單摘要](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. 按一下 [編輯資訊]，然後遵循指示更新付款資訊。
   
    ![選取編輯資訊](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>取消外部服務訂單
如果想要取消外部服務訂單，您需要在 [Azure 入口網站](https://portal.azure.com)中刪除資源。

![刪除資源](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果您仍有其他問題，請 [連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 以快速解決您的問題。




<!--HONumber=Nov16_HO3-->


