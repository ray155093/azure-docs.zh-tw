---
title: 教學課程：Azure Active Directory 與 YouEarnedIt 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 YouEarnedIt 之間的單一登入。
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-youearnedit"></a>教學課程：Azure Active Directory 與 YouEarnedIt 整合
在本教學課程中，您會了解如何整合 YouEarnedIt 與 Azure Active Directory (Azure AD)。

YouEarnedIt 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制誰有 YouEarnedIt 的存取權
* 您可以讓使用者透過其 Azure AD 帳戶自動登入 YouEarnedIt (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定與 YouEarnedIt 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 YouEarnedIt 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 YouEarnedIt
2. 設定並測試 Azure AD 單一登入

## <a name="adding-youearnedit-from-the-gallery"></a>從資源庫新增 YouEarnedIt
若要設定 YouEarnedIt 與 Azure AD 整合，您需要從資源庫將 YouEarnedIt 加入到受管理的 SaaS 應用程式清單。

**若要從資源庫加入 YouEarnedIt，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **YouEarnedIt**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_01.png)
7. 在結果窗格中，選取 [YouEarnedIt]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_06.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 YouEarnedIt 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 YouEarnedIt 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 YouEarnedIt 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法，是將 Azure AD 中**使用者名稱**的值，指派為 YouEarnedIt 中 **Username** 的值。

若要設定及測試對 YouEarnedIt 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 YouEarnedIt 測試使用者](#creating-a-predictix-price-reporting-test-user)** - 在 YouEarnedIt 中建立一個與 Azure AD 中代表 Britta Simon 的項目連結的 Britta Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，並在您的 YouEarnedIt 應用程式中設定單一登入。

**若要設定與 YouEarnedIt 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在傳統入口網站的 **YouEarnedIt** 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 YouEarnedIt] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_04.png) 
   
    a. 在 [登入 URL] 文字方塊中，使用以下模式輸入使用者登入您的 YouEarnedIt 應用程式時所使用的 URL： 
   
   * 沙箱環境︰ `https://<company name>.sandbox.youearnedit.com/users/sign_in`
   * 生產環境︰ `https://<company name>.youearnedit.com/users/sign_in`
     
     b. 按 [下一步] 
4. 在 [設定在 YouEarnedIt 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_05.png)
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
   
    b. 按 [下一步]。
5. 若要為您的應用程式設定 SSO，請連絡 YouEarnedIt 支援小組，並提供下列資訊：
   
    • 下載的 **憑證**
   
    • **SAML SSO URL**
6. 在傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
7. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟： ![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步]。
6. 在 [使用者設定檔] 對話方塊頁面上，執行下列步驟：![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_06.png) 
   
   a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
   b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下 [完成]。   

### <a name="creating-an-youearnedit-test-user"></a>建立 YouEarnedIt 測試使用者
在本節中，您要在 YouEarnedIt 中建立名為 Britta Simon 的使用者。 請與 YouEarnedIt 支援小組合作，在 YouEarnedIt 平台中加入使用者。

> [!NOTE]
> YouEarnedIt 預期識別提供者會在 NameID 屬性中提供 EmailAddress 或 UserName。 如果在資料庫中找不到對應的 UserName 或 EmailAddress 或者不完全相符，則驗證會失敗。 這會要求在 SSO 整合之前，將帳戶匯入 YouEarnedIt 系統 (通常透過 API 或 CSV 匯入)。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會把 YouEarnedIt 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 YouEarnedIt，請執行以下步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [YouEarnedIt] 。
   
    ![設定單一登入](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 YouEarnedIt 圖格時，應該會自動登入您的 YouEarnedIt 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


