---
title: "教學課程：Azure Active Directory 與 Trello 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Trello 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 36cc4206b81312f8f4ff287cd36d9dba56611fde


---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>教學課程：Azure Active Directory 與 Trello 整合
在本教學課程中，您將了解如何整合 Trello 與 Azure Active Directory (Azure AD)。

Trello 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Trello 的人員
* 您可以讓使用者利用自己的 Azure AD 帳戶，來自動登入 Trello (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Trello 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 **Trello** 單一登入功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Trello
2. 設定並測試 Azure AD 單一登入

## <a name="adding-trello-from-the-gallery"></a>從資源庫新增 Trello
如要設定將 Trello 整合到 Azure AD 中，您需要從資源庫把 Trello 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Trello，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]

6. 在搜尋方塊中，輸入 **Trello**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/tutorial_trello_01.png)

7. 在結果窗格中，選取 [Trello]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/tutorial_trello_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Trello 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Trello 與 Azure AD 中互相對應的使用者。 換句話說，必須要建立某位 Azure AD 使用者與 Trello 中相關使用者之間的連結關聯性。
建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 Trello 中 **Username** 的值。 若要設定及測試與 Trello 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Trello 測試使用者](#creating-a-the-funding-portal-test-user)** - 使 Trello 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標，是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 Trello 應用程式中設定單一登入。

Trello 應用程式預期 SAML 判斷提示會包含特定屬性。 請設定此應用程式的下列屬性。 您可以從應用程式的 [屬性]  索引標籤來管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。

![設定單一登入](./media/active-directory-saas-trello-tutorial/tutorial_trello_03.png) 

**若要使用 Trello 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站中的 **Trello** 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。
   
    ![設定單一登入][5]

2. 在 [SAML Token 屬性]  對話方塊上，針對下表中顯示的每一列執行下列步驟：

    | 屬性名稱 | 屬性值 |
    | --- | --- |    
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. 按一下 [新增使用者屬性] 來開啟 [新增使用者屬性] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-trello-tutorial/tutorial_trello_05.png)

    b. 在 [屬性名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 在 [屬性值] 清單中，選取該資料列所顯示的值。

    d. 按一下頁面底部的 [新增] 。 然後，按一下頁面底部的 [套用變更]。

1. 按一下頂端功能表中的 [快速啟動] 。
   
    ![設定單一登入][6]

2. 在傳統入口網站的 [Trello] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][7] 

3. 在 [要如何讓使用者登入 Trello] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-trello-tutorial/tutorial_trello_06.png)

4. 在 [設定應用程式設定] 對話方塊頁面上，如果您想要以 **IDP 起始模式**設定應用程式，請執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-trello-tutorial/tutorial_trello_07.png)

    a. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://trello.com/auth/saml/consume/<enterprise>`。

    b. 按 [下一步] 。

    > [!NOTE]
    > 您應該會從 Trello 取得 **\<enterprise\>** slug。 如果您沒有 Slug 值，請利用 <mailto:support@trello.com> 連絡 Trello 支援小組以取得貴企業的 Slug。
    > 
    > 

1. 如果您想要在 [設定應用程式設定] 對話方塊頁面上以 **SP 起始模式**設定應用程式，請按一下 [顯示進階設定 (選擇性)]，然後輸入**登入 URL**：
   
    ![設定單一登入](./media/active-directory-saas-trello-tutorial/tutorial_trello_08.png)
   
    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://trello.com/auth/saml/consume/<enterprise>`
   
    b. 依序按一下 [ **下一步**

2. 於 [在 Trello 設定單一登入] 頁面上，按 [下載憑證]，然後將檔案儲存在您的電腦中。
   
    ![設定單一登入](./media/active-directory-saas-trello-tutorial/tutorial_trello_09.png)

3. 若要取得為應用程式設定的 SSO，請移至 [Trello 企業 SSO 組態](https://trello.com/sso-configuration) 頁面，將「登入 URL」傳送給 Trello 小組並附加下載的憑證。
4. 在傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]

5. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/create_aaduser_09.png) 

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。

6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/create_aaduser_06.png) 
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。
   
    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-trello-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-a-trello-test-user"></a>建立 Trello 測試使用者
在本節中，您會在 Trello 中建立名為 Britta Simon 的使用者。 在本節中，您會在 Trello 中建立名為 Britta Simon 的使用者。 Trello 支援即時佈建，而且會在您第一次從 Azure AD 登入時建立新的帳戶。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會將 Trello 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Trello，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Trello] 。
   
    ![設定單一登入](./media/active-directory-saas-trello-tutorial/tutorial_trello_10.png) 

3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 

4. 在 [所有使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 Trello 圖格時，應該會自動登入您的 Trello 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-trello-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-trello-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-trello-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-trello-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-trello-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trello-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trello-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


