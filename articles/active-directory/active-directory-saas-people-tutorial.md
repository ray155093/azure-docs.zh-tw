---
title: "教學課程：Azure Active Directory 與 People 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 People 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: eb24c4fe5f382a30acc524065f2ee9e3a27ce94f


---
# <a name="tutorial-azure-active-directory-integration-with-people"></a>教學課程：Azure Active Directory 與 People 整合
本教學課程旨在說明如何整合 People 與 Azure Active Directory (Azure AD)。

People 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 People 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 People (單一登入)
* 您可以使用 Azure 傳統入口網站集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 People 整合，您需要下列項目：

* Azure 訂用帳戶
* 已啟用 People 單一登入 (SSO) 功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 People
2. 設定並測試 Azure AD 單一登入

## <a name="adding-people-from-the-gallery"></a>從資源庫新增 People
若要設定將 People 整合到 Azure AD 中，您需要從資源庫將 People 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 People，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **People**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/tutorial_people_01.png)
7. 在結果窗格中，選取 [People]，然後按一下 [完成] 來加入應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/tutorial_people_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 People 搭配運作的 Azure AD 單一登入。

若要設定及測試與 People 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 People 測試使用者](#creating-a-people-test-user)** - 使 People 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 People 應用程式中設定單一登入。

**若要設定與 People 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [People] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    [設定單一登入][6] 
2. 在 [您希望使用者如何登入 People] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-people-tutorial/tutorial_people_03.png) 
3. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟，然後按 [下一步]：
   
    ![設定單一登入](./media/active-directory-saas-people-tutorial/tutorial_people_04.png) 
   
   1. 在 [登入 URL] 文字方塊中，以下列模式輸入使用者用來登入您 People 應用程式的 URL：**“https://\<company name\>.peoplehr.com/”**。 
   2. 如果您不知道租用戶 URL，請透過 [customerservices@peoplehr.com](mailto:customerservices@peoplehr.com) 連絡 People 支援小組來取得。    3. 在 [識別碼] 文字方塊中，輸入租用戶 URL。 
   4. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL： "**https://itgs.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx**"。
   5. 按 [下一步] 。
4. 在 [設定在 People 單一登入] 頁面上，執行下列步驟，然後按 [下一步]：
   
    ![設定單一登入](./media/active-directory-saas-people-tutorial/tutorial_people_05.png) 
   
   1. 按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。
   2. 按 [下一步] 。
5. 若要取得為應用程式設定的 SSO，您必須以系統管理員身分登入 People 租用戶。
   
   1. 在左側功能表中，按一下 [設定]。
    ![設定單一登入](./media/active-directory-saas-people-tutorial/tutorial_people_001.png)    
   2. 按一下 [公司]。
   
    ![設定單一登入](./media/active-directory-saas-people-tutorial/tutorial_people_002.png) 
    3. 在 [上傳單衣登入 SAML 中繼資料檔案] 上，按一下 [瀏覽] 上傳下載的中繼資料檔案。
   
    ![設定單一登入](./media/active-directory-saas-people-tutorial/tutorial_people_003.png)
6. 在 Azure 傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
7. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 傳統入口網站中建立一個名為 Britta Simon 的測試使用者。
在 [使用者] 清單中，選取 [Britta Simon] 。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立 People 測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/create_aaduser_09.png)
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/create_aaduser_05.png) 
   
    1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
    2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
    3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/create_aaduser_06.png) 
   
   1. 在 [名字] 文字方塊中，輸入 **Britta**。  
   2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   4. 在 [角色] 清單中選取 [使用者]。
   5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-people-tutorial/create_aaduser_08.png) 
   
   1. 記下 [新密碼] 的值。
   2. 按一下頁面底部的 [新增] 。   

### <a name="creating-a-people-test-user"></a>建立 People 測試使用者
本節目標是在 People 中建立名為 Britta Simon 的使用者。 People 不支援 just-in-time 佈建，因此您需要連絡 People 支援小組以手動建立使用者。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是授與 Britta Simon 對 People 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 People，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取[People] 。
   
    ![設定單一登入](./media/active-directory-saas-people-tutorial/tutorial_people_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。
當您在「存取面板」中按一下 [People] 圖格時，應該會自動登入您的 People 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-people-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-people-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-people-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-people-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-people-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-people-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-people-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-people-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-people-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-people-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-people-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-people-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-people-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->


