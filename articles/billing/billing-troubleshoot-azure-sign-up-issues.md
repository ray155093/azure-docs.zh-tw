---
title: "針對 Azure 註冊問題進行疑難排解 | Microsoft Docs"
description: "說明如何疑難排解一些常見 Azure 登入問題。"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 459d0526961d03b564a35178f9ff4ff2e343e08c
ms.openlocfilehash: 70a9d96e36f5e386e8b1c0036ef8e2f770924930
ms.lasthandoff: 03/02/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>針對 Azure 的註冊問題進行疑難排解
如果您無法註冊 Azure，您可以檢查幾個項目來對問題進行疑難排解。

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>[依據卡片進行身分識別驗證] 區段中的進度列停止回應

進行 Azure 註冊時，有一個稱為 [依據卡片進行身分識別驗證] 的區段。 如果進度列停止回應：

![註冊時 [依據卡片進行身分識別驗證] 區段停止回應的螢幕擷取畫面](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

當您的瀏覽器封鎖協力廠商 Cookie 時便會發生此問題。

### <a name="suggestion"></a>建議

1. 在您的瀏覽器設定中允許協力廠商 Cookie。
  * 在 Edge 中，移至 [設定] -> [檢視進階設定] -> [Cookie]，選取 [不要封鎖 Cookie]。
  * 在 Chrome 中，移至 [設定] -> [顯示進階設定] -> [隱私權] -> [內容設定]，將 [封鎖第三方 Cookie 和網站資料] 取消勾選。
2. 重新整理 Azure 登入頁面，然後檢查問題是否已獲得解決。
3. 如果重新整理並未解決問題，請結束瀏覽器並重新啟動，然後再試一次。

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>註冊帳戶驗證期間沒有文字訊息或呼叫
* 最長可能需要四分鐘才能傳遞文字代碼。
* 請確認您的電話號碼可以接收簡訊。
* 再次確認您輸入的電話號碼，包括下拉式功能表中的國碼選項。
* 請確定您的電話可以收到簡訊 (SMS) (如果您使用 [傳送簡訊]) 或接聽來電 (如果您選擇 [撥號給我] 替代方式)。
* 當您收到簡訊，請在文字方塊中插入代碼，然後按一下 [驗證] 按鈕以繼續。

### <a name="suggestions"></a>建議
* 如果您的電話未接收到簡訊 (SMS)，請使用 [撥號給我] 替代驗證方法。
* 如果使用簡訊和 [撥號給我] 方法的電話驗證步驟均失敗，請使用另一個電話號碼。
* 您無法使用 VOIP 電話號碼進行電話驗證程序。

## <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒絕或不被接受
請確定註冊時使用的付款方法是支援的付款方法。 您也可以深入了解為何[在 Azure 註冊時信用卡或金融卡會遭到拒絕](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="free-trial-is-not-available"></a>「無法使用免費試用版」
過去曾使用 Azure 訂用帳戶嗎？ Azure 使用規定協議限制免費試用啟動僅適用 Azure 的新使用者。 如果您有任何其他類型的 Azure 訂用帳戶，會無法啟用免費試用。

### <a name="suggestion"></a>建議
* 請考慮註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)。

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>無法啟用 MSDN、BizSpark、BizSparkPlus 或 MPN 之類的 Azure 權益方案
請確定您使用的登入認證是正確的，並透過您的權益方案頻道來確認您是否符合所選方案的資格：

* MSDN
  * 請在您的 [MSDN 帳戶頁面](https://msdn.microsoft.com/subscriptions/manage/default.aspx)驗證自己的資格狀態。
  * 如果您無法驗證自己的狀態，請連絡 [MSDN 訂閱客服中心](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * 登入 [MPN 入口網站](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) ，並驗證您的資格狀態。 如有適當的[雲端平台能力](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)，您或許能夠得到額外的好處。
  * 如果您無法驗證自己的狀態，請連絡 [MPN 支援](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)。
* BizSpark
  * 登入 [BizSpark 入口網站](https://www.microsoft.com/bizspark/default.aspx#start-two) ，確認您 BizSpark 和 BizSpark Plus 的資格狀態。
  * 如果您無法確認狀態，則可以[連絡 BizSpark 小組](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

## <a name="cant-activate-new-azure-in-open-subscription"></a>無法啟用新的 Azure in Open 訂用帳戶
您必須具備有效的 OSA 金鑰與至少一個相關聯的 Azure in Open 權杖，才能建立新的 Azure in Open 訂用帳戶。

### <a name="suggestion"></a>建議
如果您沒有 OSA 金鑰，請連絡 [Microsoft Pinpoint](http://pinpoint.microsoft.com/) 列出的其中一個 Microsoft 合作夥伴。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

