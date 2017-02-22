---
title: "教學課程：Azure Active Directory 與 OpsGenie 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 OpsGenie 之間的單一登入"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7ab60cc289d5202be4c22b4adf46bd2aff7a7d24


---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>教學課程：Azure Active Directory 與 OpsGenie 整合
本教學課程旨在說明如何整合 OpsGenie 與 Azure Active Directory (Azure AD)。

OpsGenie 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 OpsGenie 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 OpsGenie (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 OpsGenie 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 OpsGenie 單一登入的訂用帳戶

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

1. 從資源庫新增 OpsGenie
2. 設定並測試 Azure AD 單一登入

## <a name="adding-opsgenie-from-the-gallery"></a>從資源庫新增 OpsGenie
若要設定將 OpsGenie 整合到 Azure AD 中，您需要從資源庫將 OpsGenie 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 OpsGenie，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **OpsGenie**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)
7. 在結果窗格中，選取 [OpsGenie]，然後按一下 [完成] 新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 OpsGenie 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 OpsGenie 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 OpsGenie 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值，指派為 OpsGenie 中 [Username] 的值。

若要設定及測試與 OpsGenie 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 OpsGenie 測試使用者](#creating-a-opsgenie-test-user)** - 在 OpsGenie 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 OpsGenie 應用程式中設定單一登入。

**若要設定與 OpsGenie 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [OpsGenie] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 OpsGenie] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) 

    a. 在 [登入 URL] 文字方塊中，輸入使用者用來登入您 OpsGenie 應用程式的 URL (請以下列模式輸入：**“https://app.opsgenie.com/auth/login”**)。

    > [AZURE.NOTE] 如果您需要自己的登入 URL，請連絡您的 [OpsGenie 支援小組](mailto:support@opsgenie.com) 。

    b. 按 [下一步] 。


1. 在 [設定在 OpsGenie 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) 
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。 我們將需要此憑證和「中繼資料 URL」(實體識別碼、SSO 登入 URL 及登出 URL) 來設定 OpsGenie 端的 SSO。
   
    b. 按 [下一步] 。
2. 開啟另一個瀏覽器執行個體，然後以系統管理員身分登入 OpsGenie。
3. 按一下 [設定]，然後按一下 [單一登入] 索引標籤。
   
    ![OpsGenie 設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 
4. 若要啟用 SSO，請選取 [啟用] 。
   
    ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
5. 在 [提供者] 區段中，按一下 [Azure Active Directory] 索引標籤。
   
    ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
6. 在 [Azure Active Directory] 對話方塊頁面上，執行下列步驟：
   
    ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) 
   
    a. 在 Azure 傳統入口網站的 [設定在 OpsGenie 單一登入]對話方塊頁面上，複製 [單一登入服務 URL] 的值，然後貼到 [SAML 2.0 端點] 文字方塊中。
   
    b. 從您下載的憑證建立 Base-64 編碼檔案。      
   
   > [!NOTE]
   > 如需詳細資訊，請參閱 [如何將二進位憑證轉換成文字檔](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be)。
   > 
   > 
   
    c. 在記事本中開啟 Base-64 編碼的憑證、將其內容複製到剪貼簿，然後將它貼到 [X.500 憑證] 文字方塊中。
   
    d. 按一下 [儲存變更] 。
7. 在 Azure 傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
8. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 傳統入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 
   
   a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
   b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-a-opsgenie-test-user"></a>建立 OpsGenie 測試使用者
本節的目標是要在 OpsGenie 中建立一個名為 Britta Simon 的使用者。 

1. 在 Web 瀏覽器視窗中，以系統管理員身分登入您的 OpsGenie 租用戶。
2. 按一下左側面板中的 [使用者]  以瀏覽至 [使用者] 清單。
   
   ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 
3. 按一下 [加入使用者]。
4. 在 [加入使用者]  對話方塊中，執行下列步驟：
   
   ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 
   
   a. 在 [電子郵件] 文字方塊中，輸入 Britta 在 Azure Active Directory 中的電子郵件地址。
   
   b. 在 [全名] 文字方塊中，輸入 **Britta Simon**。
   
   c. 按一下 [儲存] 。 

Britta 將會收到一封指示如何設定她的設定檔的電子郵件。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是授與 Britta Simon 對 OpsGenie 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 OpsGenie，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [OpsGenie] 。
   
    ![設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在「存取面板」中按一下 [OpsGenie] 磚時，應該會自動登入您的 OpsGenie 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


