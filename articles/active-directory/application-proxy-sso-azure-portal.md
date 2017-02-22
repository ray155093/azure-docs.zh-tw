---
title: "使用 Azure AD 應用程式 Proxy 設定應用程式的單一登入 | Microsoft Docs"
description: "在 Azure 入口網站中使用 Azure AD 應用程式 Proxy，為您已發佈的內部部署應用程式啟動單一登入。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fcec43ad50b9c78443ada116b9ca444605c4730
ms.openlocfilehash: fa2a2f8ee61b6edf92db8d1f92ab9aca8bc673c5

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>使用 Azure AD 應用程式 Proxy 提供單一登入 - 公開預覽

Azure Active Directory 應用程式 Proxy 可發佈內部部署應用程式，讓遠端員工也可以安全地存取它們，進而幫助您改進生產力。 在 Azure 入口網站中，您也可以設定這些應用程式的單一登入 (SSO)。 現在，您的使用者只需要向 Azure AD 驗證，便可以存取您的企業應用程式，而不必再次登入。

我們將在本文中使用密碼型應用程式的範例，示範密碼儲存庫存如何提供簡單的 SSO 體驗。 

您應該已經使用應用程式 Proxy 發行並測試您的應用程式。 如果還沒，請依照[使用 Azure AD 應用程式 Proxy 發佈應用程式 - 公開預覽](application-proxy-publish-azure-portal.md)的步驟操作，然後回到這裡。 

或者，如果您是應用程式 Proxy 的新手，可至[如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)深入了解這個功能。

## <a name="set-up-password-vaulting-for-your-application"></a>為應用程式設定密碼儲存庫存

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [Azure Active Directory]  >  [企業應用程式]  >  [所有應用程式]。
3. 從清單中選取您要設定 SSO 的應用程式。 如果您有很多應用程式，您可以使用搜尋方塊來篩選出正確的一個。  
4. 在 [管理] 區段中，按一下 [單一登入]。

   ![選取單一登入](./media/application-proxy-sso-azure-portal/select-sso.png)

5. 在 SSO 模式中，選擇 [密碼型登入]。
6. 在登入 URL 輸入頁面 URL，使用者將在該頁面輸入其使用者名稱和密碼以登入您的應用程式。 這應該是您透過應用程式 Proxy 發佈應用程式時建立的外部 URL。 

   ![選擇密碼型登入並輸入您的 URL](./media/application-proxy-sso-azure-portal/password-sso.png)

7. 選取 [ **儲存**]。

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>測試應用程式

移至[MyApps 網站](https://myapps.microsoft.com)，選取您剛才設定的應用程式。 使用該應用程式的專用認證登入 (或您設定有存取權之測試帳戶的認證)。 一旦您成功登入，應該能夠離開應用程式後再回到應用程式，不需要再次輸入您的認證。 

## <a name="next-steps"></a>後續步驟

閱讀其他實作[使用應用程式 Proxy 進行單一登入](active-directory-application-proxy-sso-using-kcd.md)的做法



<!--HONumber=Jan17_HO2-->


