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
ms.date: 03/21/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a50b4fb4b19acf270aaf94fea613e745ec2af649
ms.lasthandoff: 03/22/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>針對 Azure 的註冊問題進行疑難排解
如果您無法註冊 Azure，請使用此文章中的祕訣來針對常見問題進行疑難排解。 如果在註冊期間遇到信用卡的問題，請參閱[在 Azure 註冊時信用卡或金融卡遭到拒絕](billing-credit-card-fails-during-azure-sign-up.md)。 如果您有 Azure 帳戶但無法登入，請參閱[我無法登入來管理我的 Azure 訂用帳戶](billing-cannot-login-subscription.md)。

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>[依據卡片進行身分識別驗證] 區段中的進度列停止回應

若要使用卡片完成身分識別驗證，您必須允許瀏覽器接受第三方 Cookie。

![註冊時 [依據卡片進行身分識別驗證] 區段停止回應的螢幕擷取畫面](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

使用下列步驟來更新瀏覽器的 Cookie 設定。

1. 如果您是使用 Chrome，請移至 [設定]  > [顯示進階設定] > [隱私權] > [內容設定]。 取消選取 [封鎖第三方 Cookie 和網站資料]。
2. 如果您是使用 Edge，請移至 [設定]  > [檢視進階設定][Cookie] > 。 選取 [不要封鎖 Cookie]。
3. 重新整理 Azure 登入頁面，然後檢查問題是否已獲得解決。
4. 如果重新整理並未解決問題，請結束瀏覽器並重新啟動，然後再試一次。

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>註冊帳戶驗證期間沒有文字訊息或呼叫
如果您選取 [傳送簡訊]，文字代碼可能會需要四分鐘才會傳送到您的手機。 當然，針對身分識別驗證，請輸入可以接收簡訊的電話號碼，而針對 [撥號給我] 選項，您必須有可以通話的電話。 您輸入的電話號碼僅用於身分識別驗證，並不會儲存為該帳戶的連絡電話。

以下是一些祕訣：
* VOIP 電話號碼無法用於電話驗證程序。
* 再次確認您輸入的電話號碼，包括您在下拉式功能表中選取的國碼 (地區碼)。
* 如果您的手機沒有收到簡訊 (SMS)，請使用 [撥號給我] 選項。
* 如果電話驗證步驟在您嘗試 [傳送簡訊] 和 [撥號給我] 選項之後仍然失敗，請使用其他電話號碼。

當您收到簡訊或來電時，請在文字方塊中輸入您收到的代碼。

## <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒絕或不被接受
虛擬卡、預付信用卡或金融卡不被視為 Azure 訂用帳戶的付款方式。 若要查看其他造成卡片被拒絕的可能原因，請參閱[在 Azure 註冊時信用卡或金融卡遭到拒絕](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="free-trial-is-not-available"></a>「無法使用免費試用版」
過去曾使用 Azure 訂用帳戶嗎？ Azure 使用規定協議限制免費試用啟動僅適用 Azure 的新使用者。 如果您有任何其他類型的 Azure 訂用帳戶，會無法啟用免費試用。 請考慮註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)。

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>無法啟用 MSDN、BizSpark、BizSparkPlus 或 MPN 之類的 Azure 權益方案
請確定您是使用正確的登入認證。 然後檢查權益方案，以確認您符合資格。 

* MSDN
  * 請在您的 [MSDN 帳戶頁面](https://msdn.microsoft.com/subscriptions/manage/default.aspx)驗證自己的資格狀態。
  * 如果您無法驗證自己的狀態，請連絡 [MSDN 訂閱客服中心](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* BizSpark
  * 登入 [BizSpark 入口網站](https://www.microsoft.com/bizspark/default.aspx#start-two) ，確認您 BizSpark 和 BizSpark Plus 的資格狀態。
  * 如果您無法驗證自己的狀態，您可以[連絡 BizSpark 小組](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)
* MPN
  * 登入 [MPN 入口網站](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) ，並驗證您的資格狀態。 如有適當的[雲端平台能力](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)，您或許能夠得到額外的好處。
  * 如果您無法驗證自己的狀態，請連絡 [MPN 支援](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)。

## <a name="cant-activate-new-azure-in-open-subscription"></a>無法啟用新的 Azure in Open 訂用帳戶
若要建立 Azure in Open 訂用帳戶，您必須具有有效的線上服務啟用 (OSA) 金鑰與至少一個相關聯的 Azure in Open 權杖。 如果您沒有 OSA 金鑰，請連絡 [Microsoft Pinpoint](http://pinpoint.microsoft.com/) 中列出的其中一個 Microsoft 合作夥伴。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

