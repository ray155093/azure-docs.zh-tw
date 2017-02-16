---
title: "如何變更用來支付 Azure 訂用帳戶的信用卡 | Microsoft Docs"
description: "描述如何變更用來支付 Azure 訂用帳戶的信用卡"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 796331fb1a6e998c76fe20e258bb969256838efc


---
# <a name="change-the-credit-card-used-to-pay-for-an-azure-subscription"></a>變更用來支付 Azure 訂用帳戶的信用卡

這篇文章說明如何新增、變更或移除與 Azure 帳戶相關聯的信用卡，以及如何付款。

<a id="addcard"></a>
## <a name="add-a-credit-card-as-a-payment-method"></a>新增信用卡作為付款方式

請遵循下列步驟將信用卡新增至您的帳戶。

1. 以帳戶管理員登入 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions) 。
2. 在 [按一下訂用帳戶即可檢視詳細資料及使用量] 下方，選取您要新增付款方式的訂用帳戶。
3. 在頁面右側，選取 [管理付款方式] 。
4. 在 [管理付款方式] 頁面上，選取 "+" 以新增信用卡。
5. 選擇信用卡類型，並提供所有必要的資訊。
6. 選取 [下一步] 以返回 [管理付款方式] 頁面。 如果您新增的信用卡有效，它會新增至可用付款方式的清單。

> [!NOTE]
>如果您在提交信用卡資訊之後收到錯誤訊息，請參閱[拒絕或不接受您的轉帳卡或信用卡](billing-credit-card-fails-during-azure-sign-up.md)文章。
>
>

## <a name="edit-payment-information-for-an-existing-credit-card"></a>編輯現有信用卡的付款資訊

請遵循下列步驟來確認您的付款方法，並更新現有信用卡的詳細資料。

1. 以帳戶管理員登入 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions) 。

   > [!NOTE]
   > 只有帳戶管理員有權存取帳戶中心。 如需帳戶管理員的詳細資訊，請參閱[如何新增或變更 Azure 管理員角色](billing-add-change-azure-subscription-administrator.md)。
   >
   >
2. 在 [按一下訂用帳戶即可檢視詳細資料及使用量] 下方，選取連結至信用卡的訂用帳戶。</br> ![selectsub](./media/billing-how-to-change-credit-card/selectsub.png)
3. 在頁面右側，選取 [管理付款方式] 。</br> ![changesub](./media/billing-how-to-change-credit-card/changesub_new.png)

  您會看到畫面上列出目前的付款方法。 選取它來驗證必要資訊。

  **如何更新現有信用卡的詳細資料**

  如果您的信用卡換卡，但號碼維持不變，則只要更新現有信用卡的詳細資料即可，例如有效期限。 但是，如果您的信用卡號碼因為卡片遺失、遭竊或過期而變更過，您必須[將信用卡新增至您的帳戶](#addcard)。 這是因為信用卡號碼變更時，卡片上的 CVV 安全碼也會變更。

4. 在 [管理付款方式] 頁面上，選取信用卡號碼旁邊的 [編輯]。</br> ![changesub](./media/billing-how-to-change-credit-card/editcard_new.png)
5. 在 [編輯]  頁面上，確認您已選取正確的信用卡類型與信用卡號碼。
6. 對信用卡詳細資料進行您需要的變更，然後選取 [下一步] 以返回 [選擇付款方式] 頁面。

## <a name="change-the-credit-card-that-you-use-to-pay-an-azure-bill"></a>變更用來支付 Azure 帳單的信用卡

1. 以帳戶管理員登入 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions) 。
2. 在 [按一下訂用帳戶即可檢視詳細資料及使用量] 下方，選取您要更新付款方式的訂用帳戶。
3. 在頁面右側，選取 [管理付款方式] 。
4. 在 [管理付款方式] 頁面上，按一下 [改用] 來選取您要使用的信用卡。

## <a name="remove-a-credit-card-from-the-account"></a>從帳戶中移除信用卡
1. 以帳戶管理員登入 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions) 。
2. 在 [按一下訂用帳戶即可檢視詳細資料及使用量] 下方，選取您要更新付款方式的訂用帳戶。
3. 在頁面右側，選取 [管理付款方式] 。
4. 在 [管理付款方式] 頁面上，按一下 [刪除] 來選取您要刪除的信用卡。

   > [!NOTE]
   > 您無法刪除與其他使用中 Microsoft 訂用帳戶相關聯的信用卡。 如果您收到信用卡已使用的錯誤，請確定它已與您擁有的所有使用中 Microsoft 訂用帳戶取消關聯。
   >
   >

##  <a name="how-to-make-payments"></a>如何付款

如果您已設定信用卡或轉帳卡作為付款方法，則會自動扣款。
如果您使用[發票付款方法](https://azure.microsoft.com/pricing/invoicing/)，請將您的款項匯到發票底部所列的位置。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。



<!--HONumber=Dec16_HO2-->


