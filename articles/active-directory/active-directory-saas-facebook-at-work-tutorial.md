---
title: "教學課程：Azure Active Directory 與 Workplace by Facebook 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Workplace by Facebook 之間的單一登入。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: efd29518fc207eb3e554f1389a6109ae4f1f3205
ms.openlocfilehash: 252e7b3c198a3433ecd21aef2222b2ca14b7ac04


---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>教學課程：Azure Active Directory 與 Workplace by Facebook 整合
本教學課程旨在說明如何整合 Workplace by Facebook 與 Azure Active Directory (Azure AD)。

將 Workplace by Facebook 與 Azure AD 整合可提供下列優點： 

* 您可以在 Azure AD 中控制可存取 Workplace by Facebook 的人員 
* 您可以自動佈建帳戶已獲授與存取 Workplace by Facebook 的使用者
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Workplace by Facebook (單一登入)
* 您可以在單一中央位置管理您的帳戶 

若您想了解 SaaS 應用程式與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 CS Stars 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用單一登入的 Workplace by Facebook

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。 

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>從資源庫新增 Workplace by Facebook
若要設定將 Workplace by Facebook 整合到 Azure AD 中，您需要從資源庫將 Workplace by Facebook 新增到受管理的 SaaS 應用程式清單中。

**若要從資源庫新增 Workplace by Facebook，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Workplace by Facebook**。
7. 在結果窗格中，選取 [Workplace by Facebook]，然後按一下 [完成] 以新增應用程式。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure Active Directory 帳戶驗證至 Workplace by Facebook。

**若要設定透過 Workplace by Facebook 使用 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站中加入 Workplace by Facebook 後，按一下 [設定單一登入] 。
2. 在 [設定應用程式 URL] 畫面上，輸入使用者將登入您的 Workplace by Facebook 應用程式的 URL。 這是您的 Workplace by Facebook 租用戶 URL (範例：https://example.facebook.com/)。 完成後按 [下一步] 。
3. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Workplace by Facebook 公司網站。
4. 請遵循下列 URL 的指示，將 Workplace by Facebook 設定為使用 Azure AD 作為身分識別提供者：[https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. 完成後，返回顯示 Azure 傳統入口網站的瀏覽器視窗，按一下核取方塊以確認您已完成此程序，然後按 [下一步] 和 [完成]。


## <a name="automatically-provisioning-users-to-workplace-by-facebook"></a>自動佈建使用者至 Workplace by Facebook
Azure AD 支援將指派的使用者的帳戶詳細資料自動同步處理至 Workplace by Facebook 的功能。 這個自動同步處理可讓 Workplace by Facebook 在使用者嘗試第一次登入之前，取得授權使用者存取所需的資料。 當存取在 Azure AD 中被撤銷時，它也會從 Workplace by Facebook 取消佈建使用者。

自動佈建可以透過在 Azure 傳統入口網站視窗中按一下 [設定帳戶佈建]  來設定。

如需如何設定自動佈建的詳細資訊，請參閱 [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-workplace-by-facebook"></a>將使用者指派給 Workplace by Facebook
若要讓佈建的 AAD 使用者能夠在其存取面板上看見 Workplace by Facebook，它們必須獲指派 Azure 傳統入口網站內的存取。

**若要將使用者指派給 Workplace by Facebook：**

1. 在 Azure 傳統入口網站中的 Workplace by Facebook 開始頁面上，按一下 [指派帳戶]。
2. 在 [顯示] 功能表中，選取您是否想要將使用者或群組指派給 Workplace by Facebook，然後按一下核取記號按鈕。
3. 在結果清單中，選取您要指派 Workplace by Facebook 的使用者或群組。
4. 在頁面頁尾中，按一下 [指派]  按鈕。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png







<!--HONumber=Nov16_HO5-->


