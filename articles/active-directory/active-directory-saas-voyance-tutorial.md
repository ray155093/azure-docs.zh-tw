---
title: "教學課程：Azure Active Directory 與 Voyance 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Voyance 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d316178b9d3451eaefe88a8245538aef42b57a85
ms.openlocfilehash: 466b16cf391f6de101b6d212ad51d296d77cc804


---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>教學課程：Azure Active Directory 與 Voyance 整合

在本教學課程中，您將了解如何整合 Voyance 與 Azure Active Directory (Azure AD)。

Voyance 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Voyance 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Voyance (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Voyance 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Voyance 單一登入的訂用帳戶


> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Voyance
2. 設定並測試 Azure AD 單一登入


## <a name="adding-voyance-from-the-gallery"></a>從資源庫新增 Voyance
若要設定 Voyance 與 Azure AD 整合，您需要從資源庫將 Voyance 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Voyance，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 

    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。

    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。

    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。

    ![應用程式][4]

6. 在搜尋方塊中，輸入 **Voyance**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_01.png)

7. 在結果窗格中，選取 [Voyance]，然後按一下 [完成] 以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Voyance 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Voyance 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Voyance 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法是指派 Azure AD 中**使用者名稱**的值作為 Voyance 中 **Username** 的值。

若要設定及測試與 Voyance 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Voyance 測試使用者](#creating-a-voyance-test-user)** - 在 Voyance 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，然後在您的 Voyance 應用程式中設定單一登入。


**若要設定與 Voyance 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在傳統入口網站中的 **Voyance** 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入][6]

2. 在 [要如何讓使用者登入 Voyance] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
 
    ![設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_02.png)

3. 在 [設定應用程式設定] 對話方塊頁面上，如果您想要以 **IDP 起始模式**設定應用程式，請執行下列步驟，然後按 [下一步]：

    ![設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_03.png)

    a. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://<company name>.nyansa.com`。

    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://<company name>.nyansa.com/saml/create/`。

    c. 按 [下一步] 。

4. 如果您想要在 [設定應用程式設定] 對話方塊頁面上以 **SP 起始模式**設定應用程式，則請按一下 [顯示進階設定 (選擇性)]，然後輸入**登入 URL** 並按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_04.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<company name>.nyansa.com/`。

    b. 按一下頁面底部的 [新增] 來單一登入應用程式。

    > [!NOTE]
    > 請注意，您必須使用實際的「登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 若要取得這些值，請連絡 [Voyance 支援小組](emaiLto:support@nyansa.com)。

5. 於 [在 Voyance 設定單一登入] 頁面上，按 [下載憑證]，然後將檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_05.png) 

6. 在不同的網頁瀏覽器視窗中，以管理員身分登入您的 Voyance 租用戶。

7. 移至瀏覽列右上角，然後按一下 [Acme 大學]。
    
    ![在應用程式端設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_001.png) 

8. 按一下 [系統管理員設定]。

    ![在應用程式端設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_002.png)

9. 按一下 [使用者存取] 索引標籤

    ![在應用程式端設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_003.png)

10. 按一下 [已停用 SSO] 按鈕，將 Azure AD 設定為使用 SAML 2.0 的 IdP。

    ![在應用程式端設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_004.png)

11. 移至 [SAML v2] 區段並執行下列步驟︰

    ![在應用程式端設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_005.png)

    a. 選取 [啟用] 。

    b.這是另一個 C# 主控台應用程式。 在 [IdP 登入 URL] 文字方塊中，輸入取自於 Azure AD 應用程式組態精靈的 [SAML SSO URL] 值。

    c. 在記事本中開啟您的 Base64 編碼的憑證，將其內容複製到剪貼簿，然後貼到 [IdP 憑證] 文字方塊。

    d. 按一下 [儲存]  按鈕。

12. 在傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。

    ![Azure AD 單一登入][10]

13. 在 [單一登入確認] 頁面上，按一下 [完成]。  
  
    ![Azure AD 單一登入][11]


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/create_aaduser_09.png) 

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/create_aaduser_05.png) 

    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c. 按 [下一步] 。

6.  在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/create_aaduser_06.png) 

    a. 在 [名字] 文字方塊中，輸入 **Britta**。  

    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。

    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d. 在 [角色] 清單中選取 [使用者]。

    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-voyance-tutorial/create_aaduser_08.png) 

    a. 記下 [新密碼] 的值。

    b. 按一下頁面底部的 [新增] 。   



### <a name="creating-a-voyance-test-user"></a>建立 Voyance 測試使用者

本節的目標是要在 Voyance 中建立一個名為 Britta Simon 的使用者。 Voyance 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 嘗試存取 Voyance 時，如果使用者還不存在，就會建立新使用者。

> [!NOTE]
> 如果您需要手動建立使用者，就必須連絡 [Voyance 支援小組](emaiLto:support@nyansa.com)。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Voyance 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Voyance，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Voyance]。

    ![設定單一登入](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_50.png) 

3. 在頂端的功能表中，按一下 [使用者] 。

    ![指派使用者][203] 

4. 在 [使用者] 清單中，選取 [Britta Simon] 。

5. 在底部的工具列中，按一下 [指派] 。
    
    ![指派使用者][205]



### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Voyance 圖格時，應該會自動登入您的 Voyance 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


