---
title: "針對 Azure 註冊問題進行疑難排解 | Microsoft Docs"
description: "說明如何疑難排解一些常見 Azure 登入問題。"
services: 
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e2e22b767ce1d54e7b90158fb6adb877e3a165b8


---
# <a name="i-cant-sign-up-for-azure"></a>我無法註冊 Azure
如果您無法註冊 Azure，您可以檢查幾個項目來對問題進行疑難排解。

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>註冊帳戶驗證期間沒有文字訊息或呼叫
* 請確認您的電話號碼可以接收簡訊。
* 再次確認您輸入的電話號碼，包括下拉式功能表中的國碼選項。
* 請確定您的電話可以收到簡訊 (SMS) (如果您使用 [傳送簡訊]) 或接聽來電 (如果您選擇 [撥號給我] 替代方式)。
* 如果您使用行動電話，請確定電話連線能力良好。
* 如果您選擇 [傳送簡訊]，最多等候四分鐘，傳訊系統會將文字代碼傳送給您。
* 當您收到簡訊，請在文字方塊中插入代碼，然後按一下 [驗證] 按鈕以繼續。

### <a name="suggestions"></a>建議
* 如果您的電話未接收到簡訊 (SMS)，請使用 [撥號給我] 替代驗證方法。
* 如果使用簡訊和 [撥號給我] 方法的電話驗證步驟均失敗，請使用另一個電話號碼。
* 您無法使用 VOIP 電話號碼進行電話驗證程序。

> [!NOTE]
> 您可以在稍後更新 [設定檔資訊](billing-how-to-change-azure-account-profile.md)，以變更您的慣用電話號碼。
> 
> 

## <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒絕或不被接受
請確定註冊時使用的付款方法是 Azure 啟用或付款所支援的方法。

* 不接受虛擬和預付的信用卡/轉帳卡。
* 根據帳戶國家/地區而定，接受的信用卡/轉帳卡提供者有所不同。

### <a name="suggestion"></a>建議
如需使用信用卡或金融卡的註冊問題常見原因，請參閱[在 Azure 註冊時信用卡或金融卡遭到拒絕](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>無法啟用 MSDN、BizSpark、BizSparkPlus 或 MPN 之類的 Azure 權益方案
請透過您的權益方案頻道驗證，來確認您是否符合所選方案的資格：

* MSDN
  * 請在您的 [MSDN 帳戶頁面](https://msdn.microsoft.com/subscriptions/manage/default.aspx)驗證自己的資格狀態。
  * 如果您無法驗證自己的狀態，請連絡 [MSDN 訂閱客服中心](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * 登入 [MPN 入口網站](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) ，並驗證您的資格狀態。 如有適當的[雲端平台能力](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)，您或許能夠得到額外的好處。
  * 如果您無法驗證自己的狀態，請連絡 [MPN 支援](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)。
* Bizpark
  * 登入 [BizSpark 入口網站](https://www.microsoft.com/bizspark/default.aspx#start-two) ，確認您 BizSpark 和 BizSpark Plus 的資格狀態。
  * 如果無法驗證您的狀態，請傳送電子郵件到 [連絡 BizSpark 小組](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

### <a name="suggestion"></a>建議
如果您嘗試啟用新的權益訂用帳戶，而且在註冊期間遇到任何錯誤，請確認您已在 [Azure 訂用帳戶頁面](http://account.windowsazure.com/Subscriptions)完成訂用帳戶設定。 可能需要幾分鐘，訂用帳戶才會顯示為作用中。 啟動您的訂用帳戶時，您會收到一封電子郵件。 如果您的訂用帳戶狀態持續擱置超過四分鐘，請[連絡 Azure 支援](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以取得協助。

## <a name="cant-activate-new-azure-in-open-subscription"></a>無法啟用新的 Azure in Open 訂用帳戶
您必須具備有效的 OSA 金鑰與至少一個相關聯的 Azure in Open 權杖，才能啟用新的 Azure in Open 訂用帳戶。

### <a name="suggestion"></a>建議
如果您沒有 OSA 金鑰，請連絡 [Microsoft Pinpoint](http://pinpoint.microsoft.com/) 列出的其中一個 Microsoft 合作夥伴。

## <a name="cant-activate-azure-free-trial"></a>無法啟用 Azure 免費試用
過去曾使用 Azure 訂用帳戶嗎？ Azure 使用規定協議限制免費試用啟動僅適用 Azure 的新使用者。 如果您有任何其他類型的 Azure 訂用帳戶，會無法啟用免費試用。

### <a name="suggestion"></a>建議
* 如果您過去已啟用 Azure 訂用帳戶，以致於免費試用啟用失敗，請考慮「隨用隨付」訂用帳戶。 
* 請檢查您是否有資格獲得權益優惠。 請在 [Microsoft Azure 優惠詳細資料頁面](https://azure.microsoft.com/support/legal/offer-details/) 深入了解。 權益方案需要特定的必要條件。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。 




<!--HONumber=Nov16_HO3-->


