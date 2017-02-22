---
title: "無法登入 Azure 訂用帳戶 | Microsoft Docs"
description: "說明如何疑難排解一些常見 Azure 訂用帳戶的登入問題。"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: fc1c8cb6b7c186e189658cacf218ec7e9261472a


---
# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>我無法登入來管理我的 Azure 訂用帳戶
本文將引導您完成一些可解決登入問題的最常見方法。

## <a name="page-hangs-in-the-loading-status"></a>頁面在載入狀態停止回應
如果您的網際網路瀏覽器頁面停滯，請嘗試下列每個步驟，直到您可以進入 [Azure 入口網站](https://portal.azure.com)為止。

* 重新整理頁面。
* 使用不同的網際網路瀏覽器。
* 如果您使用的是 Microsoft Internet Explorer，請使用「InPrivate 瀏覽」模式來瀏覽至 Azure 入口網站。 
  
  A.    按一下 [工具] ![工具按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > [安全性] > [InPrivate 瀏覽]。
  
  B.    瀏覽至 [Azure 入口網站](https://portal.azure.com)，然後登入入口網站。

## <a name="error-message-no-subscriptions-found"></a>錯誤訊息「找不到任何訂用帳戶」
如果您的帳戶沒有足夠的權限，可能會看到**找不到訂用帳戶**錯誤訊息。 請確定您以正確的系統管理員身分登入。 帳戶管理員只能存取[帳戶中心](https://account.windowsazure.com/Subscriptions)。 服務管理員 (SA) 和共同管理員 (CA) 只能存取 [Azure 入口網站](https://portal.azure.com)或 Azure 傳統入口網站。

**案例 1：在 [Azure 入口網站](https://portal.azure.com)收到錯誤訊息**

若要解決此問題，請為帳戶 [新增共同管理員或擁有者角色](billing-add-change-azure-subscription-administrator.md) 。

**案例 2：在 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)收到錯誤訊息**

請檢查您所使用的帳戶是否為帳戶管理員。 若要確認誰是帳戶管理員，請依照下列步驟操作︰

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [訂用帳戶] 。
3. 選取您想要檢查的訂用帳戶，然後選取 [設定] 。
4. 選取 [屬性] 。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>自動將您登入為不同的使用者
如果您在網際網路瀏覽器中使用多個使用者帳戶，就可能發生此問題。

若要解決此問題，請嘗試下列其中一個方法：

* 清除快取並刪除網際網路 Cookie。 在 Internet Explorer 中，按一下 [工具] ![工具按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > [網際網路選項] > [刪除]。 確定已選取暫存檔、Cookie、密碼及瀏覽歷程記錄的核取方塊，然後按一下 [刪除]。
* 重設 Internet Explorer 設定來還原您所做的任何個人設定。 按一下 [工具]![工具按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png)> [網際網路選項] > [進階] > 選取 [刪除個人設定] 方塊 > [重設]。
* 以「InPrivate 模式」瀏覽至 Azure 入口網站。 按一下 [工具] ![工具按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > [安全性] > [InPrivate 瀏覽]。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以快速解決您的問題。 




<!--HONumber=Feb17_HO2-->


