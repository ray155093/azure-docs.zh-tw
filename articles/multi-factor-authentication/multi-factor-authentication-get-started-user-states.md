---
title: "Microsoft Azure Multi-Factor Authentication 使用者狀態"
description: "了解 Azure MFA 中的使用者狀態"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 3079969783f589cb90f17eac116c5d57884db49f
ms.lasthandoff: 02/17/2017


---
# <a name="user-states-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的使用者狀態
Azure Multi-Factor Authentication 中的使用者帳戶具有下列三種不同狀態：

| State | 說明 | 受影響的非瀏覽器應用程式 | 
|:---:|:---:|:---:|
| 已停用 |未註冊 Azure Multi-Factor Authentication (MFA) 之新使用者的預設狀態。 |否 |
| 已啟用 |已在 Azure MFA 中註冊使用者，但使用者尚未註冊。 系統將在他們下一次登入時提示他們註冊。 |不會。  它們會繼續運作，直到註冊程序完成為止。 |
| 已強制 |已註冊使用者，而且使用者已完成 Azure MFA 的註冊程序。 |是。  應用程式需要應用程式密碼。 |

## <a name="changing-a-user-state"></a>變更使用者狀態
使用者的狀態會反映系統管理員是否已在 Azure MFA 中註冊他們，以及他們是否已完成註冊程序。

所有使用者一開始都是「已停用」狀態。 當您在 Azure MFA 中註冊使用者時，他們的狀態會變更為「已啟用」。 當已啟用的使用者登入並完成註冊程序之後，他們的狀態就會變更為「已強制」。  

### <a name="view-user-states"></a>檢視使用者狀態

使用下列步驟來存取您可在其中檢視並管理使用者狀態的頁面：

1. 以系統管理員身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 選取左邊的 [Active Directory] 。
3. 針對您要檢視的使用者選取目錄。
   ![選取目錄 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. 選取 [使用者]。
5. 在頁面底部，選取 [管理 Multi-Factor Auth]。 
   ![選取 [管理 Multi-Factor Auth] - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. 隨即開啟新的索引標籤，以顯示使用者狀態。
   ![Multi-Factor Authentication 使用者狀態 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-state-from-disabled-to-enabled"></a>將狀態從已停用變更為已啟用

1. 使用上述步驟來取得 Multi-Factor Authentication使用者頁面。 
2. 尋找您想要針對 Azure MFA 啟用的使用者。 您可能需要在頂端變更檢視。 確定狀態為 [已停用]。
   ![尋找使用者 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. 勾選其名稱旁的方塊。
4. 在右邊的快速步驟中，按一下 [啟用]。
   ![啟用所選的使用者 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/user1.png)
5. 選取 [啟用 Multi-Factor Auth]。
   ![啟用 Multi-Factor Auth - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/enable2.png)
6. 請注意，使用者的狀態已經從 [已停用] 變更為 [已啟用]。
   ![查看現在已啟用該使用者 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/user.png)

當您啟用使用者之後，應透過電子郵件通知他們。 請包含將在他們下次登入時要求他們註冊的細節，以及一些非瀏覽器應用程式可能無法使用雙步驟驗證的資訊。 您也可以包含我們所提供 [Azure MFA 使用者指南](./end-user/multi-factor-authentication-end-user.md)的連結，以協助他們開始使用。 

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>若要將狀態從啟用/強制變更為停用

1. 使用[檢視使用者狀態](#view-user-states)中的步驟，來取得 Multi-Factor Authentication 使用者頁面。
6. 尋找您想要設為停用的使用者。 您可能需要在頂端變更檢視。 請確定狀態為 [已停用] 或 [已強制]。
7. 勾選其名稱旁的方塊。
8. 在右邊的快速步驟中，按一下 [停用]。
   ![停用使用者 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
9. 系統會提示您確認此動作。 按一下 [是] 。
10. 如果使用者已順利停用，您會收到成功訊息。 按一下 [關閉] 。


