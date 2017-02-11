---
title: "教學課程：Azure Active Directory 與 Recognize 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Recognize 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 43e9febd4bc230db69b55ae3f5bb13ff17b2e3f6


---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>教學課程：Azure Active Directory 與 Recognize 整合
本教學課程旨在說明如何整合 Recognize 與 Azure Active Directory (Azure AD)。

將 Recognize 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制誰能夠存取 Recognize
* 您可以讓您的使用者使用其 Azure AD 帳戶自動登入 Recognize (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Recognize 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 Recognize 單一登入的訂用帳戶

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

1. 從資源庫新增 Recognize
2. 設定並測試 Azure AD 單一登入

## <a name="adding-recognize-from-the-gallery"></a>從資源庫新增 Recognize
若要設定將 Recognize 整合至 Azure AD，您必須將資源庫中的 Recognize 新增至您的受管理 SaaS 應用程式清單中。

**若要從資源庫新增 Recognize，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中輸入 **Recognize**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)
7. 在結果面板中，選取 [Recognize]，然後按一下 [完成] 以新增應用程式。
   
    ![選取資源庫中的應用程式](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，來使用 Recognize 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Recognize 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Recognize 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值，指派為 Recognize 中 [使用者名稱] 的值。

若要使用 Recognize 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Recognize 測試使用者](#creating-a-recognize-test-user)** - 使 Recognize 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，並在您的 Recognize 應用程式中設定單一登入。

**若要使用 Recognize 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在傳統入口網站的 **Recognize** 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 Recognize] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)
3. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟，然後按 [下一步]：
   
    ![設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)
   
    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://recognizeapp.com/<your-domain>/saml/sso`。
   
    b. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://recognizeapp.com/<your-domain>/saml/metadata`。
   
    c. 依序按一下 [ **下一步**
   
   > [!NOTE]
   > 如果您不知道這些 URL，請使用範例模式輸入範例 URL。 若要取得這些值，您可以參考「步驟 9」以取得詳細資料，或透過 <mailto:support@recognizeapp.com> 與 Recognize 支援小組連絡。
   > 
   > 
4. 於 **Configure single sign-on at Recognize** (在 Recognize 設定單一登入) 頁面上，按一下 [下載憑證]，然後將該檔案儲存在您的電腦上：
   
    ![設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)
5. 在不同的網頁瀏覽器視窗中，以管理員身分登入您的 Recognize 租用戶。
6. 按一下右上角的 [功能表]。 移至 [Company Admin] (公司管理員)。
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)
7. 在左側的導覽窗格上，按一下 [設定] 。
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)
8. 在 [SSO Settings] (SSO 設定) 區段下執行下列步驟。
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
   
    a. 將 **[啟用 SSO]** 選取為 **ON**。
   
    b. 在 [IDP 實體識別碼] 文字方塊中，輸入 Azure AD 應用程式組態精靈中 [簽發者 URL]  的值。
   
    c. 在 [SSO 目標 URL] 文字方塊中，輸入 Azure AD 應用程式組態精靈中 [單一登入服務 URL] 的值。
   
    d. 在 [SLO 目標 URL] 文字方塊中，輸入 Azure AD 應用程式組態精靈中 [單一登出服務 URL] 的值。
   
    e. 在記事本中開啟您下載的憑證檔，將其內容複製到剪貼簿上，然後貼到 [憑證]  文字方塊中。 
   
    f. 按一下 [儲存設定]  按鈕。 
9. 在 [SSO Settings] 區段中，複製 [Service Provider Metadata url] 下方的 URL。
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)
10. 在空白瀏覽器下方開啟**中繼資料 URL 連結**，以下載中繼資料文件。 然後在 [設定應用程式設定] 對話方塊上，使用 Recognize 提供給您的 EntityDescriptor 值作為**識別碼**。
    
    ![在應用程式端設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)
11. 在傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
    
    ![Azure AD 單一登入][10]
12. 在 [單一登入確認] 頁面上，按一下 [完成]。  
    
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)
   
   a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
   b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-a-recognize-test-user"></a>建立 Recognize 測試使用者
若要讓 Azure AD 使用者能夠登入 Recognize，必須將他們佈建到 Recognize。 在 Recognize 的情況下，佈建是手動工作。

此應用程式不支援 SCIM 佈建，但有能夠佈建使用者的替代使用者同步處理作業。 

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要佈建使用者帳戶，請執行下列步驟：
1. 以管理員身分登入您的 Recognize 公司網站。
2. 按一下右上角的 [功能表]。 移至 [Company Admin] (公司管理員)。
3. 在左側的導覽窗格上，按一下 [設定] 。
4. 在 [使用者同步處理] 區段中執行下列步驟。
   
   ![新使用者](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "New User")
   
   a. 對於 [已啟用同步處理] 選取 **ON**。
   
   b. 對於 [Choose sync provider] (選擇同步處理提供者)，選取 [Microsoft / Office 365]。
   
   c. 按一下 [Run User Sync] (執行使用者同步處理)

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節目標是授與 Britta Simon 對 Recognize 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200]

**若要將 Britta Simon 指派給 Recognize，請執行以下步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201]
2. 在應用程式清單中，選取 [Recognize] 。
   
    ![設定單一登入](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。

當您在 [存取面板] 中按一下 [Recognize] 圖格時，應該會自動登入您的 Recognize 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


