---
title: "教學課程：Azure Active Directory 與 Samanage 整合 | Microsoft Docs"
description: "了解如何使用 Samanage 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da49f1d9f8aa5fbbaf6e16f1db03a16f80a2bc92


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>教學課程：Azure Active Directory 與 Samanage 整合
本教學課程旨在說明如何整合 Samanage 與 Azure Active Directory (Azure AD)。

Samanage 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Samanage 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Samanage (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Samanage 的整合作業，您需要下列項目：

* 有效的 Azure 訂閱
* Samanage 中的租用戶

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

1. 從資源庫新增 Samanage
2. 設定並測試 Azure AD 單一登入

## <a name="adding-samanage-from-the-gallery"></a>從資源庫新增 Samanage
若要設定 Samanage 與 Azure AD 的整合作業，您必須從資源庫將 Samanage 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Samanage，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Add an application from gallerry")
6. 在 [搜尋方塊]中，輸入 **Samanage**。
   
   ![應用程式庫](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Application gallery")
7. 在結果窗格中，選取 [Samanage]，然後按一下 [完成] 來新增應用程式。
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節目標是說明如何以名為「Britta Simon」的測試使用者為基礎，設定及測試透過 Samanage 使用 Azure AD 單一登入。

若要使單一登入作用，Azure AD 必須知道 Samanage 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Samanage 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值，指派為 Samanage 中 **Username** 的值。

若要使用 Samanage 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Samanage 測試使用者](#creating-a-Samanage-test-user)** - 使 Samanage 中，Britta Simon 的對應使用者連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，並在您的 Samanage 應用程式中設定單一登入。

**若要使用 Samanage 設定 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [Samanage] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Configure single sign-on")
2. 在 [要如何讓使用者登入 Samanage] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![Microsoft Azure AD 單一登入](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Microsoft Azure AD Single Sign-On")
3. 在 [設定 App 設定] 對話方塊頁面執行下列步驟：
   
   ![設定應用程式 URL](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Configure App URL")
   
   a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<Company Name>.samanage.com/saml_login/<Company Name>`。
   
   b. 按 [下一步] 
   
   > [!NOTE]
   > 請注意這些不是真正的值。 您必須以實際的登入 URL 更新這些值。 若要取得這些值，請參閱步驟 8.c 以取得詳細資訊，或連絡 Samanage。
   > 
   > 
4. 在 [設定在 Samanage 單一登入] 頁面上，按一下 [下載憑證]，然後將憑證檔案儲存在您的電腦上。
   
   ![設定單一登入](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Configure Single Sign-On")
5. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Samanage 公司網站。
6. 按一下 [儀表板] 並選取左側導覽窗格中的 [安裝]。
   
   ![儀表板](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")
7. 按一下 [單一登入] 。
   
   ![單一登入](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")
8. 瀏覽至 [使用 SAML 登入]  區段，執行下列步驟︰
   
   ![使用 SAML 登入](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
   
   a.  按一下 [使用 SAML 啟用單一登入]。
   
   b.  在 [識別提供者 URL] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [識別提供者識別碼] 值。    
   
   c.  確認 [登入 URL] 符合步驟 3 的 [登入 URL]。
   
   d.  在 [登出 URL] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [遠端登出 URL] 值。
   
   e. 在 [SAML 簽發者] 文字方塊中，輸入識別提供者中所設定的應用程式識別碼 URI。
   
   f.  在記事本中開啟您的 base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [在下面貼上識別提供者 x.509 憑證] 文字方塊中。
   
   g.  按一下 [若 Samanage 中不存在使用者則加以建立]。
   
   h.  按一下 [更新] 。
9. 在傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
   
   ![設定單一登入](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Configure Single Sign-On")
10. 在 [單一登入確認] 頁面上，按一下 [完成]。
    
    ![設定單一登入](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 測試使用者](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png)
   
   a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
   b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-a-samanage-test-user"></a>建立 Samanage 測試使用者
若要讓 Azure AD 使用者可以登入 Samanage，則必須將他們佈建至 Samanage。Samanage 需以手動的方式佈建。

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要佈建使用者帳戶，請執行下列步驟：
1. 以系統管理員身分登入您的 Samanage 公司網站。
2. 按一下 [儀表板] 並選取左側導覽窗格中的 [安裝]。
   
   ![設定](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Setup")
3. 按一下 [使用者]  索引標籤
   
   ![使用者](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Users")
4. 按一下 [新使用者] 。
   
   ![新使用者](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "New User")
5. 輸入您想要佈建之 Azure AD 帳戶的 [名稱] 與 [電子郵件地址]，然後按一下 [建立使用者]。
   
   ![建立使用者](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Creat User")
   
   > [!NOTE]
   > AAD 帳戶的持有者會收到一封電子郵件，並遵循連結在啟用其帳戶前進行確認。 您可以使用任何其他的 Samanage 使用者帳戶建立工具或 Samanage 提供的 API 來佈建 AAD 使用者帳戶。
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是授與 Britta Simon 對 Samanage 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Assign users")

**若要將 Britta Simon 指派給 Samanage，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Assign users")
2. 在應用程式清單中，選取 [Samanage] 。
   
    ![設定單一登入](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Assign users")
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 [Samanage] 圖格時，應該會自動登入您的 Samanage 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


