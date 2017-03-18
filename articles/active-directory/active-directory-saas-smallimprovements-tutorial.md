---
title: "教學課程：Azure Active Directory 與 Small Improvements 整合| Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Small Improvements 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 3599d44a3349efbc62dde97a7862a0656d1eb226
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>教學課程：Azure Active Directory 與 Small Improvements 整合
本教學課程旨在說明如何整合 Small Improvements 與 Azure Active Directory (Azure AD)。  

Small Improvements 與 Azure AD 整合提供下列優點：

* 您可在 Azure AD 中控制可存取 Small Improvements 的人員
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Small Improvements 單一登入 (SSO)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Small Improvements 的整合作業，需要下列項目：

* Azure AD 訂用帳戶
* 啟用 Small Improvements SSO 功能的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD SSO。  

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Small Improvements
2. 設定並測試 Azure AD SSO

## <a name="adding-small-improvements-from-the-gallery"></a>從資源庫新增 Small Improvements
若要設定 Small Improvements 與 Azure AD 的整合作業，您需要從資源庫將 Small Improvements 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Small Improvements，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Small Improvements**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_01.png)
7. 在結果窗格中，選取 [Small Improvements]，然後按一下 [完成] 以加入應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO
本節目標是示範如何根據名為「Britta Simon」的測試使用者，使用 Small Improvements 設定及測試 Azure AD SSO。

若要讓 SSO 運作，Azure AD 必須知道 Small Improvements 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Small Improvements 中相關使用者之間的連結關聯性。  

建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值，指派為 Small Improvements 中 **Username** 的值。

若要使用 Small Improvements 設定及測試 Azure AD SSO，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Small Improvements 的測試使用者](#creating-a-small-improvements-test-user)** - 在 Small Improvements 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD SSO，並在您的 Small Improvements 應用程式中設定 SSO。

**若要使用 Small Improvements 設定 Azure AD SSO，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [Small Improvements] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 Small Improvements] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_04.png) 
   
     >[!NOTE]
     >請透過 [Support@small-improvements.com](mailto:support@small-improvements.com) 連絡 Small Improvements 支援小組，來設定您帳戶的網域名稱。 必須這麼做才能讓 SSO 運作。
     >  

  1. 在 [登入 URL] 文字方塊中，輸入使用者用來登入您 Small Improvements 應用程式的 URL。  
  2. 按 [下一步] 。
4. 在 [設定在 Small Improvements 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_05.png) 
 1. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。  
 2. 按 [下一步] 。
2. 在另一個瀏覽器視窗中，以系統管理員身分登入您的 Small Improvements 公司網站。
3. 在主要儀表板頁面上，按一下左側的 [系統管理]  按鈕。
   
    ![設定單一登入](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) 
4. 按一下 [整合] 區段的 [SAML SSO] 按鈕。
   
    ![設定單一登入](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png) 
5. 在 [SSO 設定] 頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png)  
 1. 在 Azure 傳統入口網站的 [設定在 Small Improvements 單一登入] 對話方塊頁面上，複製 [SAML SSO URL] 的值，然後將它貼到 [HTTP 端點] 文字方塊中。  
 2. 在記事本中開啟您下載的憑證，然後複製憑證內容，再把該內容貼到 [x509 憑證] 文字方塊中。 
 3. 如果您想讓使用者能夠使用 SSO 和登入表單驗證，請選取 [啟用也能透過登入/密碼來存取] 選項。  
 4. 在 [SAML 提示] 文字方塊中，輸入適當的值來為 SSO 登入按鈕命名。  
 5. 按一下 [儲存] 。
6. 在 Azure 傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
7. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 傳統入口網站中建立一個名為 Britta Simon 的測試使用者。  

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_05.png) 
 1. 針對 [使用者類型]，選取 [您組織中的新使用者]。  
 2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。 
 3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_06.png)  
 1. 在 [名字] 文字方塊中，輸入 **Britta**。   
 2. 在 [姓氏] 文字方塊中，輸入 **Simon**。 
 3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。 
 4. 在 [角色] 清單中選取 [使用者]。 
 5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_08.png) 
 1. 記下 [新密碼] 的值。 
 2. 按一下 [完成]。   

### <a name="create-a-small-improvements-test-user"></a>建立 Small Improvements 的測試使用者
本節目標是在 Small Improvements 中建立名為 Britta Simon 的使用者。

您已經在＜ [設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)＞一節中啟用該功能。

**若要在 Small Improvements 中建立名為 Britta Simon 的使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 Small Improvements 公司網站。
2. 按一下首頁左側功能表中的 [系統管理] 。
3. 按一下 [使用者管理] 區段的 [使用者目錄]  按鈕。 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) 
4. 按一下 [加入小組成員] 。
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) 
5. 在 [名字]、[姓氏] 及 [電子郵件地址] 文字方塊中，輸入您想要佈建之使用者的相關資訊。 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png) 
6. 您也可以選擇在 [傳送通知電子郵件]  方塊中，輸入您個人的訊息。 如果您不想傳送通知，則取消選取此核取方塊。
7. 按一下 [建立使用者] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節目標是授與 Britta Simon 對 Small Improvements 的存取權，讓她能夠使用 Azure SSO。

    ![Assign User][200] 

**若要將 Britta Simon 指派到 Small Improvements，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Small Improvements] 。
   
    ![設定單一登入](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD SSO 組態。  

當您在存取面板中按一下 [Small Improvements] 磚時，應該會自動登入 Small Improvements 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png

