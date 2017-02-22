---
title: "系統管理員管理使用者和裝置 - Azure MFA | Microsoft Docs"
description: "說明如何變更使用者設定，如強迫使用者重複 proof-up 程序。"
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: yossib
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 27ff53646992308d574dcc2e631cd63b8227f9c8
ms.openlocfilehash: 826fc2b2eaaf180d922c7a9a4c329ec4379c2ae0


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理雲端中 Azure Multi-Factor Authentication 的使用者設定
身為管理員，您可以管理下列使用者和裝置設定。  

* [要求選定使用者再次提供連絡方法](#require-selected-users-to-provide-contact-methods-again)
* [刪除使用者現有的應用程式密碼](#delete-users-existing-app-passwords)
* [還原使用者所有暫停之裝置的 MFA](#restore-mfa-on-all-suspended-devices-for-a-user)

當電腦或裝置遺失、遭竊或您需要移除使用者存取權限時，這是很實用的方法。

## <a name="require-selected-users-to-provide-contact-methods-again"></a>要求選定使用者再次提供連絡方法
此設定會強制使用者在登入時再次完成註冊程序。 請注意，如果使用者擁有非瀏覽器應用程式的應用程式密碼，這些應用程式仍然可以繼續運作。  您也可以選取 [ **刪除選定使用者產生的所有現有應用程式密碼**] 來刪除使用者的應用程式密碼。

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>如何要求使用者再次提供連絡方法
1. 登入 Azure 傳統入口網站。
2. 在左側按一下 [Active Directory]。
3. 在 [目錄] 下方，針對欲要求再次提供連絡方法的使用者按一下目錄。
4. 在頂端按一下 [使用者]。
5. 在頁面底部，按一下 [管理 Multi-Factor Auth]。 這會開啟 Multi-Factor Authentication 頁面。
6. 尋找要管理的使用者，並在名稱旁的方塊中打勾。 您可能需要在頂端變更檢視。
7. 這會在右側顯示 [管理使用者設定]  連結。 按一下此按鈕。
8. 勾選 [要求選取的使用者再次提供連絡方法] 。
   ![提供連絡方法](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
9. 按一下 [儲存]。
10. 按一下 [關閉]

## <a name="delete-users-existing-app-passwords"></a>刪除使用者現有的應用程式密碼
這會刪除使用者建立的所有應用程式密碼。 與這些應用程式密碼相關的非瀏覽器應用程式將會停止運作，直到建立新應用程式密碼為止。

### <a name="how-to-delete-users-existing-app-passwords"></a>如何刪除使用者的現有應用程式密碼
1. 登入 Azure 傳統入口網站。
2. 在左側按一下 [Active Directory]。
3. 在 [目錄] 下方，針對要刪除應用程式密碼的使用者按一下目錄。
4. 在頂端按一下 [使用者]。
5. 在頁面底部，按一下 [管理 Multi-Factor Auth]。 這會開啟 Multi-Factor Authentication 頁面。
6. 尋找要管理的使用者，並在名稱旁的方塊中打勾。 您可能需要在頂端變更檢視。
7. 這會在右側顯示 [管理使用者設定]  連結。 按一下此按鈕。
8. 勾選 [刪除選定使用者產生的所有現有應用程式密碼] 。
   ![刪除應用程式密碼](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. 按一下 [儲存]。
10. 按一下 [關閉]。

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>在使用者已記住的所有裝置上還原 MFA
系統管理員可以在使用者裝置和瀏覽器上還原 Multi-Factor Authentication。 如果這樣做，將會從使用者的所有裝置和瀏覽器中移除記住 MFA，而使用者在下次登入時必須使用 MFA。

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>如何還原使用者所有暫停之裝置上的 MFA
1. 登入 Azure 傳統入口網站。
2. 在左側按一下 [Active Directory]。
3. 在 [目錄] 下方，針對要還原 MFA 的使用者按一下目錄。
4. 在頂端按一下 [使用者]。
5. 在頁面底部，按一下 [管理 Multi-Factor Auth]。 這會開啟 Multi-Factor Authentication 頁面。
6. 尋找要管理的使用者，並在名稱旁的方塊中打勾。 您可能需要在頂端變更檢視。
7. 這會在右側顯示 [管理使用者設定]  連結。 按一下此按鈕。
8. 勾選 [在所有記住的裝置上還原多重要素驗證]****
   ![刪除應用程式密碼](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. 按一下 [儲存]。
10. 按一下 [關閉]。



<!--HONumber=Jan17_HO4-->


