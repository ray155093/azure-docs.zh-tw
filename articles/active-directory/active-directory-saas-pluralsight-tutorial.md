---
title: 教學課程：Azure Active Directory 與 Pluralsight 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Pluralsight 之間的單一登入。
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
ms.date: 10/10/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-pluralsight"></a>教學課程：Azure Active Directory 與 Pluralsight 整合
本教學課程旨在說明如何整合 Pluralsight 與 Azure Active Directory (Azure AD)。

Pluralsight 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Pluralsight 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Pluralsight (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定與 Pluralsight 的 Azure AD 整合，您需要下列項目：

* Azure 訂用帳戶
* 啟用 Pluralsight 單一登入的訂用帳戶

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

1. 從資源庫新增 Pluralsight
2. 設定並測試 Azure AD 單一登入

## <a name="adding-pluralsight-from-the-gallery"></a>從資源庫新增 Pluralsight
若要設定 Pluralsight 與 Azure AD 整合，您需要從資源庫將 Pluralsight 加入到受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Pluralsight，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Pluralsight**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)
7. 在結果窗格中，選取 [Pluralsight]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節的目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Pluralsight 搭配運作的 Azure AD 單一登入。

若要設定及測試對 Pluralsight 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Pluralsight 測試使用者](#creating-a-pluralsight-test-user)** - 在 Pluralsight 中建立一個與 Azure AD 中代表 Britta Simon 的項目連結的 Britta Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 Pluralsight 應用程式中設定單一登入。

Pluralsight 應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 SAML token 屬性設定。 以下螢幕擷取畫面顯示上述的範例。 

![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) 

您也可以利用適當的值新增 **"Unique ID"** 屬性，例如 EmployeeID 和其他適合貴組織的值。 另請注意，這不是必要的屬性，但您可以新增它以識別唯一的使用者。 

**若要設定與 Pluralsight 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站中的 **Pluralsight** 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。
   
    ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png) 
2. 若要移除多餘的 [SAML Token 屬性] ，請執行下列步驟： 
   
    ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/2829.png) 

    a. 上表紅色方塊中的每個使用者屬性，只要將滑鼠停留在屬性上，按一下即可刪除。 




1. 若要加入必要的 [SAML Token 屬性] ，請對下表顯示的每一列執行下列步驟：
   
   | 屬性名稱 | 屬性值 |
   | --- | --- |
   | 名字 |user.givenname |
   | 姓氏 |user.surname |
   | 電子郵件 |user.mail |
   
    a. 按一下 [新增使用者屬性] 來開啟 [新增使用者屬性] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png) 

    b. 在 [屬性名稱] 文字方塊中，輸入針對該資料列顯示的屬性名稱。

    c. 從 [屬性值] 清單中，選取針對該資料列顯示的屬性值。

    d. 按一下頁面底部的 [新增] ****。  



1. 按一下 [套用變更] 。
   
    ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/3232.png)  
2. 按一下頂端功能表中的 [快速啟動] 。
   
    ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)  
3. 在 Azure 傳統入口網站的 [Pluralsight] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
4. 在 [要如何讓使用者登入 Pluralsight] 頁面上，選取 [Azure AD 單一登入]，然後按一下 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png) 
5. 在 [設定應用程式設定]  對話方塊頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) 

    a. 在 [登入 URL] 文字方塊中，使用以下模式輸入使用者登入您的 Pluralsight 應用程式時所使用的 URL：`https://<instance name>.pluralsight.com/sso/<comapny name>`

    b. 按 [下一步] 。


1. 在 [設定在 Pluralsight 單一登入] 頁面上，執行下列步驟： ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png) 
   
    a. 按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。
   
    b. 按 [下一步] 。
2. 若要為您的應用程式設定 SSO，請連絡 Pluralsight [專業服務](mailTo:professionalservices@pluralsight.com) 小組，並提供下載的中繼資料檔案。
3. 在 Azure 傳統入口網站中，選取單一登入設定確認項目，然後按一下 [下一步] 。
   
    ![Azure AD 單一登入][10]
4. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

在 [使用者] 清單中，選取 [Britta Simon] 。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按一下頁面底部的 [新增] ****來單一登入應用程式。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) 
   
   a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
   b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按一下頁面底部的 [新增] ****來單一登入應用程式。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] ****。   

### <a name="creating-a-pluralsight-test-user"></a>建立 Pluralsight 測試使用者
本節目標是在 Pluralsight 中建立名為 Britta Simon 的使用者。 請與 Pluralsight 支援小組合作，在 Pluralsight 帳戶中新增使用者。 

> [!NOTE]
> 如果您需要手動建立使用者，您需要連絡 Pluralsight 支援小組。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是要授與 Britta Simon 對 Pluralsight 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Pluralsight，請執行以下步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Pluralsight] 。
   
    ![設定單一登入](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 Pluralsight 圖格時，應該會自動登入您的 Pluralsight 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


