---
title: "教學課程：Azure Active Directory 與 Proofpoint on Demand 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Proofpoint on Demand 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5c6129050dacf098fa60439175b91b3a2d219182


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>教學課程：Azure Active Directory 與 Proofpoint on Demand 整合
在本教學課程中，您會了解如何整合 Proofpoint on Demand 與 Azure Active Directory (Azure AD)。

Proofpoint on Demand 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Proofpoint on Demand 的人員。
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Proofpoint on Demand (單一登入或 SSO)。
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶。

若您想了解 SaaS 應用程式與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Proofpoint on Demand 的整合，您需要下列項目：

* Azure AD 訂用帳戶
* Proofpoint on Demand 單一登入的訂用帳戶

若要測試本教學課程中的步驟，請遵循下列建議：

* 除非必要，否則請勿使用生產環境。
* 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Proofpoint on Demand。
2. 設定和測試 Azure AD 單一登入。

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>從資源庫新增 Proofpoint on Demand
若要設定將 Proofpoint on Demand 整合到 Azure AD 中，您需要從資源庫將 Proofpoint on Demand 新增到受管理的 SaaS 應用程式清單。

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory 圖示][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表上的 [應用程式] 。
   
    ![[應用程式] 功能表項目][2]
4. 按一下頁面底部的 [新增]。
   
    ![新增按鈕][3]
5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫中新增應用程式]。
   
    ![從資源庫新增應用程式的選項][4]
6. 在搜尋方塊中，輸入 **Proofpoint on Demand**。
   
    ![您在其中輸入 "Proofpoint on Demand" 的方塊](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. 在結果窗格中，選取 [Proofpoint on Demand]，然後按一下 [完成] 以新增應用程式。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入
在本節中，您會以名為 Britta Simon 的測試使用者為基礎，設定及測試透過 Proofpoint on Demand 使用 Azure AD 單一登入功能。

若要讓單一登入能夠運作，Azure AD 必須知道 Proofpoint on Demand 與 Azure AD 中互相對應的使用者。 換句話說，您必須建立 Azure AD 使用者和 Proofpoint on Demand 中相關使用者之間的連結關聯性。

將 Azure AD 中**使用者名稱**的值指派為 Proofpoint on Demand 中 **Username** 的值，即可建立此連結關聯性。

若要設定及測試透過 Proofpoint on Demand 使用 Azure AD 單一登入功能，請完成下列程序：

1. [設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
3. [建立 Proofpoint on Demand 測試使用者](#creating-a-proofpoint-ondemand-test-user)，在 Proofpoint on Demand 中建立 Britta Simon 的對應項目，且該項目必須與 Azure AD 中代表 Britta Simon 的項目連結。
4. [指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [測試單一登入](#testing-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，並在您的 Proofpoint on Demand 應用程式中設定單一登入。

1. 在傳統入口網站的 **Proofpoint on Demand** 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![[設定單一登入] 按鈕][6]
2. 在 [您希望使用者如何登入 Proofpoint on Demand] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![[Microsoft Azure AD 單一登入] 選項按鈕](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. 在 [設定應用程式設定]  頁面上，執行下列步驟：
   
    ![方塊已填入資料的 [設定應用程式設定] 頁面](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   
    a. 在 [登入 URL] 文字方塊中，輸入使用者用來登入 Proofpoint on Demand 應用程式的 URL。 使用下列模式︰**https://\<hostname\>.pphosted.com/ppssamlsp_hostname**
   
    b. 在 [識別碼] 方塊中，使用下列模式輸入 URL：**https://\<hostname/>.pphosted.com/ppssamlsp**
   
    c. 在 [回覆 URL] 方塊中，使用下列模式輸入 URL：**https://\<hostname/>.pphosted.com:portnumber/v1/samlauth/samlconsumer**
   
    d. 按 [下一步] 。
4. 在 [設定在 Proofpoint on Demand 單一登入] 頁面上，執行下列步驟：
   
    ![包含 [下載憑證] 按鈕的 [設定在 Proofpoint on Demand 單一登入] 頁面](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
   
    b. 按 [下一步] 。
5. 若要為您的應用程式設定 SSO，請聯絡 Proofpoint on Demand 支援小組並提供下列資訊：
   
    • 下載的憑證
   
    • 實體識別碼
   
    • SAML SSO URL
6. 在傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
    ![用來確認您已設定單一登入的核取方塊][10]
7. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![確認電子郵件][11]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名為 Britta Simon 的測試使用者。

![使用者清單中的測試使用者][20]

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory 圖示](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者]。
   
    ![[使用者] 功能表項目](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. 若要開啟 [新增使用者] 對話方塊，請按一下 [新增使用者]。
   
    ![[新增使用者] 按鈕](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. 在 [告訴我們這位使用者] 頁面上，執行下列步驟： ![方塊已填入資料的 [告訴我們這位使用者] 頁面](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)
   
    a. 在 [使用者類型] 方塊中，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔] 頁面上，執行下列步驟：![方塊已填入資料的 [使用者設定檔] 頁面](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)
   
   a. 在 [名字] 方塊中輸入 **Britta**。  
   
   b. 在 [姓氏] 方塊中輸入 **Simon**。
   
   c. 在 [顯示名稱] 方塊中輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按 [下一步] 。
7. 在 [取得暫時密碼] 頁面上，按一下 [建立]。
   
    ![用來建立暫時密碼的按鈕](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. 在 [取得暫時密碼]  頁面上，執行下列步驟：
   
    ![包含密碼資訊的 [取得暫時密碼] 頁面](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)
   
    a. 記下 [新密碼] 方塊中的值。
   
    b. 按一下 [完成]。   

### <a name="create-a-proofpoint-on-demand-test-user"></a>建立 Proofpoint on Demand 測試使用者
在本節中，您會在 Proofpoint on Demand 中建立名為 Britta Simon 的使用者。 請與 Proofpoint on Demand 支援小組合作，在 Proofpoint on Demand 平台中新增使用者。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會將 Proofpoint on Demand 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![使用者資訊，顯示透過直接方法啟用的存取][200]

1. 在傳統入口網站的目錄檢視中，按一下頂端功能表上的 [應用程式] 以開啟應用程式檢視。
   
    ![[應用程式] 功能表項目][201]
2. 在應用程式清單中選取 **Proofpoint on Demand**。
   
    ![已選取 Proofpoint on Demand 的應用程式清單](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. 在頂端的功能表上，按一下 [使用者]。
   
    ![[使用者] 功能表項目][203]
4. 在 [使用者] 清單中，選取 [Britta Simon]。
5. 在底部工具列上，按一下 [指派]。
   
    ![指派按鈕][205]

### <a name="test-single-sign-on"></a>測試單一登入
在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板上按一下 [Proofpoint on Demand] 圖格時，您應該會自動登入 Proofpoint on Demand 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


