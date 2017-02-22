---
title: "教學課程：Azure Active Directory 與 Lesson.ly 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Lesson.ly 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8c9dc6e6-5d85-4553-8a35-c7137064b928
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 33f4c13930017bdcbaf899b16d32ba84bdfbf9ee
ms.openlocfilehash: e51deb2cb890669c4800c232e2b781c5f27117f2


---
# <a name="tutorial-azure-active-directory-integration-with-lessonly"></a>教學課程：Azure Active Directory 與 Lesson.ly 整合
本教學課程旨在說明如何整合 Lesson.ly 與 Azure Active Directory (Azure AD)。

Lesson.ly 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Lesson.ly 的人員
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Lesson.ly (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Lesson.ly 與 Azure AD 的整合作業，需要下列項目：

* Azure AD 訂用帳戶
* 啟用 Lesson.ly 單一登入功能的訂用帳戶

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

1. 從資源庫加入 Lesson.ly
2. 設定並測試 Azure AD 單一登入

## <a name="adding-lessonly-from-the-gallery"></a>從資源庫加入 Lesson.ly
若要設定 Lesson.ly 與 Azure AD 的整合作業，您需要從資源庫將 Lesson.ly 加入到受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Lesson.ly，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Lesson.ly**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_01.png)
7. 在結果窗格中，選取 [Lesson.ly]，然後按一下 [完成] 以新增應用程式。

![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節目標是示範如何根據名為 "Britta Simon" 的測試使用者，使用 Lesson.ly 設定及測試 Azure AD 單一登入功能。

若要讓單一登入作用，Azure AD 必須能知道 Lesson.ly 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Lesson.ly 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法，是將 Azure AD 中**使用者名稱**的值，指派為 Lesson.ly 中 **Username** 的值。

若要使用 Lesson.ly 設定及測試 Azure AD 單一登入功能，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Lesson.ly 測試使用者](#creating-a-Lessonly-test-user)** - 讓 Britta Simon 在 Lesson.ly 中有一個對應身分連結到她的 Azure AD 代表身分。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Lesson.ly。

Lesson.ly 應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 **SAML Token 屬性** 設定。
以下螢幕擷取畫面顯示上述的範例。

![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png) 

**若要使用 Lesson.ly 設定 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 Lesson.ly 應用程式整合頁面中，按一下頂端功能表中的 [屬性] 來開啟 [SAML Token 屬性] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_07.png) 
2. 若要加入必要的屬性對應，請執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_08.png) 
   
    a. 針對上表中的每個資料列，按一下 [新增使用者屬性] 。
   
    b. 在 [屬性名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。
   
    c. 在 [屬性值]  清單中，選取該資料列所顯示的屬性名稱。
   
    d. 按一下 [完成]
3. 按一下 [套用變更] 。
4. 在您的瀏覽器中，按一下 [返回]  以再次開啟 [快速啟動] 對話方塊
5. 按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
6. 在 [要如何讓使用者登入 Lesson.ly] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_03.png) 
7. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_04.png) 

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入使用者用來登入 Lessonly 應用程式的 URL：**“https://companyname.lesson.ly/signin”**。 參考一般名稱時，**companyname** 需要由實際名稱取代。


1. 在 [設定在 Lesson.ly 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_05.png) 
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
   
    b. 按 [下一步] 。
2. 若要為您的應用程式設定 SSO，請透過 dev@lessonly.com. 將下載的憑證檔案附加至您的郵件，並與 Lesson.ly 小組分享中繼資料 URL (實體識別碼、SSO 登入 URL 和登出 URL)，以在其端設定 SSO。
3. 在 Azure 傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
   ![Azure AD 單一登入][10]
4. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
   ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 傳統入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_06.png) 
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。
   
    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lessonly-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下 [完成]。   

### <a name="creating-a-lessonly-test-user"></a>建立 Lesson.ly 測試使用者
本節的目標是要在 Lesson.ly 中建立名為 Britta Simon 的使用者。 Lesson.ly 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 嘗試存取 Lesson.ly 時，如果使用者還不存在，就會建立新使用者。 [設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)。

> [!NOTE]
> 如果您需要手動建立使用者，您需要連絡 Lesson.ly 支援小組。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是授與 Britta Simon 對 Lesson.ly 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Lesson.ly，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Lesson.ly] 。
   
    ![設定單一登入](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 Lesson.ly 圖示時，應該會自動登入 Lesson.ly 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


