---
title: "取消 Azure 訂用帳戶 | Microsoft Docs"
description: "說明如何取消 Azure 訂用帳戶，例如免費試用訂用帳戶"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a8751fa70bd500a29a3e86de57de4fb919405136
ms.lasthandoff: 03/31/2017


---
# <a name="cancel-your-subscription-for-azure"></a>取消您的 Azure 訂用帳戶
您可以用[帳戶管理員](billing-subscription-transfer.md#whoisaa)身分取消您的 Azure 訂用帳戶。 取消訂用帳戶之後，您將無法存取 Azure 服務和資源端點。

取消訂用帳戶之前︰

* 備份您的資料。 例如，如果您將資料儲存在 Azure 儲存體或 SQL，請下載複本。 如果您有虛擬機器，請將其映像儲存在本機。
* 關閉您的服務。 移至[管理入口網站中的資源頁面](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，並**停止**任何執行中的虛擬機器、應用程式或其他服務。
* 考慮移轉您的資料。 請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。

如果您取消付費 [Azure 支援方案](https://azure.microsoft.com/support/plans/)，6 個月期剩下的每個月仍會計費。

## <a name="cancel-subscription-via-the-azure-portal"></a>透過 Azure 入口網站取消訂用帳戶
1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 從左邊的 Azure 服務窗格中選取 [訂用帳戶]。

    ![顯示 [訂用帳戶] 按鈕的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/submenu.png)

3. 選取您要取消的訂用帳戶，然後按一下 [取消訂用帳戶]。

    ![顯示 [取消] 按鈕的螢幕擷取畫面](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
4. 輸入訂用帳戶名稱，然後選擇取消原因。 
5. 按一下底部的 [取消訂用帳戶] 按鈕。

## <a name="cancel-subscription-by-using-the-azure-account-center"></a>使用 Azure 帳戶中心消取消訂用帳戶
1. 以帳戶管理員身分登入 [Azure 帳戶中心](https://account.windowsazure.com/subscriptions)。
2. 在 [按一下訂用帳戶即可檢視詳細資料及使用量] 下方，選取您要取消的訂用帳戶。

    ![顯示已選取範例訂用帳戶的螢幕擷取畫面](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)
3. 選取頁面右側的 [取消訂用帳戶] 。

    ![顯示 [取消訂用帳戶] 按鈕的螢幕擷取畫面](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)
4. 選取 [是，取消我的訂用帳戶] 。

    ![顯示 [取消] 對話方塊的螢幕擷取畫面](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)
5. 按一下  ![[核取符號] 按鈕](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) 關閉對話視窗並返回您的訂用帳戶頁面。

## <a name="what-happens-after-you-cancel-your-subscription"></a>取消訂用帳戶之後會發生什麼情況？
需要 10 分鐘的時間，取消作業才會反映在入口網站中，但是會立即停止計費。

取消訂用帳戶之後，我們會等 90 天之後才永久刪除您的資料，以防您需要存取它，或您改變心意。 如需詳細資訊，請參閱 [Microsoft 信任中心 - 如何管理您的資料](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)。

## <a name="reactivate-subscription"></a>重新啟動訂用帳戶
如果您不小心取消隨用隨付訂用帳戶，您可以[在帳戶中心重新啟動它](billing-subscription-become-disable.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果您仍有問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

