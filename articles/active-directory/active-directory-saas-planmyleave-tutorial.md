---
title: "教學課程：Azure Active Directory 與 PlanMyLeave | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 PlanMyLeave 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 83334b1b02df214e51c86a862636c9392cd19474
ms.openlocfilehash: ba418a641b339a0d94a3c7b2596d37fbd88a30c5
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>教學課程：Azure Active Directory 與 PlanMyLeave 整合

在本教學課程中，您會了解如何整合 PlanMyLeave 與 Azure Active Directory (Azure AD)。

PlanMyLeave 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 PlanMyLeave 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 PlanMyLeave (單一登入)
- 您可以在 Azure 管理入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 PlanMyLeave 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 PlanMyLeave 單一登入的訂用帳戶


> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫中新增 PlanMyLeave
2. 設定並測試 Azure AD 單一登入


## <a name="adding-planmyleave-from-the-gallery"></a>從資源庫中新增 PlanMyLeave
若要設定 PlanMyLeave 與 Azure AD 整合，您需要從資源庫將 PlanMyLeave 加入到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 PlanMyLeave，請執行下列步驟：**

1. 在 **[Azure 管理入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **PlanMyLeave**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_001.png)

5. 在結果窗格中，選取 [PlanMyLeave]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 PlanMyLeave 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 PlanMyLeave 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 PlanMyLeave 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值指派為 PlanMyLeave 中 **Username** 的值。

若要使用 PlanMyLeave 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 PlanMyLeave 測試使用者](#creating-a-planmyleave-test-user)** - 使 PlanMyLeave 中 Britta Simon 的對應使用者連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 管理入口網站中啟用 Azure AD 單一登入，並在您的 PlanMyLeave 中設定單一登入。

**若要使用 PlanMyLeave 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 管理入口網站的 [PlanMyLeave] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊頁面上，選取 [SAML 型登入] 做為 [模式]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_01.png)

3. 在 [PlanMyLeave 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_02.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<company-name>.planmyleave.com/Login.aspx`
    
    b. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > 請注意這些不是真正的值。 您必須使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [PlanMyLeave 支援小組](mailto:support@planmyleave.com)，以取得這些值。

4. 在 [SAML 簽署憑證] 區段中，按一下 [建立新憑證]。

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_03.png)     

5. 在 [建立新憑證] 對話方塊中，按一下行事曆圖示並選取 [到期日]。 然後按一下 [儲存] 按鈕。

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_300.png)

6. 在 [SAML 簽署憑證] 區段中，選取 [啟用新憑證] 並按一下 [儲存] 按鈕。

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_04.png)

7. 在 [變換憑證] 快顯視窗上，按一下 [確定]。

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_400.png)

8. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_05.png) 

9. 在 [PlanMyLeave 組態] 區段上，按一下 [設定 PlanMyLeave] 以開啟 [設定登入] 視窗。

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_06.png) 

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_07.png)

10. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 PlanMyLeave 租用戶。

11. 移至 [系統設定]。 接著，在 [安全性管理] 區段上，按一下 [公司 SAML 設定]。

    ![在應用程式端設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_002.png) 

12. 在 [SAML 設定] 區段上，按一下編輯器圖示。

    ![在應用程式端設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_003.png)

13. 在 [更新 SAML 設定] 區段上，執行下列步驟：

    ![在應用程式端設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  在 [登入 URL] 文字方塊中，放入來自 Azure AD 應用程式組態視窗的 [SAML 單一登入服務 URL] 值。

    b.這是另一個 C# 主控台應用程式。  在記事本中開啟您下載的憑證檔，只將 ---Begin Certificate--- 和 ---End Certificate---- 之間的內容複製到剪貼簿上，然後貼到 [憑證]  文字方塊中。

    c. 將 "**Is Enable**" 設定為 "**Yes**"。

    d. 按一下 [儲存] 。



### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 管理入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_01.png) 

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。 



### <a name="creating-a-planmyleave-test-user"></a>建立 PlanMyLeave 測試使用者

本節目標是在 PlanMyLeave 中建立名為 Britta Simon 的使用者。 PlanMyLeave 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 嘗試存取 PlanMyLeave 時，如果使用者還不存在，就會建立新使用者。

> [!NOTE]
> 如果您需要手動建立使用者，就必須連絡 [PlanMyLeave 支援小組](mailto:support@planmyleave.com)。



### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 PlanMyLeave 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 PlanMyLeave，請執行下列步驟：**

1. 在 Azure 管理入口網站中，開啟應用程式檢視，然後瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 **PlanMyLeave**。

    ![設定單一登入](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_50.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    


### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [PlanMyLeave] 圖格時，應該會自動登入您的 PlanMyLeave 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png
