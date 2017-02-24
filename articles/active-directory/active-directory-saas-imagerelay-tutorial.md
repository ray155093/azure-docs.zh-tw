---
title: "教學課程：Azure Active Directory 與 Image Relay 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Image Relay 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a14be44b66c5e61350785ddfaf5b96125a196103
ms.openlocfilehash: 94fdae2992e69f4330d325968a9e66b3f6f38ce0


---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>教學課程：Azure Active Directory 與 Image Relay 整合
本教學課程旨在說明如何整合 Image Relay 與 Azure Active Directory (Azure AD)。  
Image Relay 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Image Relay 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Image Relay (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Image Relay 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 Image Relay 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。  
本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Image Relay
2. 設定並測試 Azure AD 單一登入

## <a name="adding-image-relay-from-the-gallery"></a>從資源庫新增 Image Relay
若要設定 Image Relay 與 Azure AD 整合，您需要從資源庫將 Image Relay 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Image Relay，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Image Relay**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)
7. 在結果窗格中，選取 [Image Relay]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 ImageRelay 來設定及測試 Azure AD 單一登入。

要啟用單一登入，Azure AD 需要代表 Image Relay 中相關的使用者的使用者帳戶。  換句話說，必須在 Azure AD 使用者與 Image Relay 中的相關使用者之間建立連結關聯性。  
建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 Image Relay 中 **Username** 的值。

若要設定及測試與 Image Relay 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Image Relay 測試使用者](#creating-a-userecho-test-user)** - 在 Image Relay 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 Image Relay 應用程式中設定單一登入。

**若要使用 Image Relay 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [Image Relay] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
     ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 Image Relay] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
     ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 
   
    a. 在 [登入 URL] 文字方塊中，輸入您的使用者用來登入 Image Relay 應用程式的 URL (例如：https://fabrikam.ImageRelay.com/)。
   
    b. 按 [下一步] 。
4. 在 [設定在 Image Relay 單一登入] 頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
   
    b. 按 [下一步] 。
5. 在另一個瀏覽器視窗中，以系統管理員身分登入您的 Image Relay 公司網站。
6. 在頂端的工具列中按一下 [使用者和權限] 工作負載。
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 
7. 按一下 [建立新的權限] 。
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 
8. 在 [單一登入設定] 工作負載中，選取 [這個群組只能透過單一登入來登入] 核取方塊，然後按一下 [儲存]。
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 
9. 移至 [帳戶設定] 。
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 
10. 移至 [單一登入設定]  工作負載。
    
     ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)
11. 在 [SAML設定]  頁面上，執行下列步驟：
    
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. 在 Azure 傳統入口網站中，複製 [單一登入服務 URL]，然後將它貼至 [登入 URL] 文字方塊中。

    b. 在 Azure 傳統入口網站中，複製 [單一登出服務 URL]，然後將它貼至 [登出 URL] 文字方塊中。

    c. 選取 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** 做為 [名稱識別碼格式]。

    d. 選取 [POST 繫結] 做為 [服務提供者要求的繫結選項 (影像轉送)]。

    e. 在 [x.509 憑證] 下方，按一下 [更新憑證]。

    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. 在記事本中開啟下載的憑證，複製其內容，然後貼到 [x.509 憑證] 文字方塊中。

    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. 在 [Just-In-Time 使用者佈建] 區段中，選取 [啟用 Just-In-Time 使用者佈建]。

    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. 選取只允許透過單一登入來登入的權限群組 (例如 [SSO 基本])。

    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. 按一下 [儲存] 。

1. 在 Azure 傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
2. 在 [單一登入確認] 頁面上，按一下 [完成]。
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 傳統入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。
   
    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下 [完成]。   

### <a name="creating-an-image-relay-test-user"></a>建立 Image Relay 測試使用者
本節目標是在 Image Relay 中建立名為 Britta Simon 的使用者。

**若要在 Image Relay 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

1. 以系統管理員身分登入您的 Image Relay 公司網站。
2. 移至 [使用者和權限]，選取 [建立 SSO 使用者]。
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 
3. 輸入您要佈建的使用者的 [電子郵件]、[名字]、[姓氏] 和 [公司]，選取只能透過單一登入來登入的權限群組 (例如 [SSO 基本])。
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 
4. 按一下 [建立] 。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節目標是授與 Britta Simon 對 Image Relay 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Image Relay，請執行以下步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Image Relay]。
   
    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。  
當您在存取面板中按一下 [Image Relay] 圖格時，應該會自動登入您的 Image Relay 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


