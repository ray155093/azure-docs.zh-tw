---
title: "教學課程：Azure Active Directory 與 Jive 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Jive 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 304ee87e82ce5acc4479fd16d3ac1aa340e42815
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>教學課程：Azure Active Directory 與 Jive 整合
在本教學課程中，您會了解如何整合 Jive 與 Azure Active Directory (Azure AD)。

Jive 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Jive
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Jive (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Jive 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 Jive 單一登入的訂用帳戶

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

1. 從資源庫新增 Jive
2. 設定並測試 Azure AD 單一登入

## <a name="adding-jive-from-the-gallery"></a>從資源庫新增 Jive
若要設定將 Jive 整合到 Azure AD 中，您需要從資源庫將 Jive 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Jive，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Jive**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. 在結果窗格中，選取 [Jive]，然後按一下 [完成] 以加入應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Jive 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Jive 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Jive 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值，指派為 Jive 中 [Username] 的值。

若要設定及測試與 Jive 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Jive 測試使用者](#creating-a-jive-test-user)** - 在 Jive 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[設定使用者佈建](#configuring-user-provisioning)** - 說明如何對 Jive 啟用 Active Directory 使用者帳戶的使用者佈建。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
6. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 Jive 應用程式中設定單一登入。

**若要設定與 Jive 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在傳統入口網站的 [Jive] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [您希望使用者如何登入 Jive] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 
   
    a. 在 [登入 URL] 文字方塊中，使用以下模式，輸入使用者登入您的 Jive 應用程式時所使用的 URL：**https://\<customer name\>.jivecustom.com**。
   
    b. 按 [下一步] 
4. 在 [設定在 Jive 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
   
    b. 按一下頁面底部的 [新增] 。
5. 以系統管理員身分登入 Jive 租用戶。
6. 在頂端的功能表中，按一下 [Saml]。
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)
   
    a. 選取 [一般] 索引標籤下的 [已啟用]。
   
    b. 按一下 [儲存所有 saml 設定] 按鈕。
7. 瀏覽至 [Idp 中繼資料]索引標籤。
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)
   
    a. 複製下載的中繼資料 XML 檔案內容，然後貼到 [識別提供者 (IDP) 中繼資料] 文字方塊中。
   
    b. 按一下 [儲存所有 saml 設定] 按鈕。 
8. 移至 [使用者屬性對應]索引標籤。
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)
   
    a. 在 [電子郵件] 文字方塊中，複製並貼上值為 **mail** 的屬性名稱。
   
    b. 在 [名字] 文字方塊中，複製並貼上值為 **givenname** 的屬性名稱。
   
    c. 在 [姓氏] 文字方塊中，複製並貼上值為 **surname** 的屬性名稱。
9. 在 Azure AD 入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   ![Azure AD 單一登入][10]
10. 在 [單一登入確認] 頁面上，按一下 [完成]。  
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟： ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。
   
    e. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-a-jive-test-user"></a>建立 Jive 測試使用者
在本節中，您要在 Jive 中建立名為 Britta Simon 的使用者。 請與 Jive 支援小組合作，在 Jive 平台中加入使用者。

### <a name="configuring-user-provisioning"></a>設定使用者佈建
本節的目的是要說明如何對 Jive 啟用 Active Directory 使用者帳戶的使用者佈建。  
在此程序中，您必須提供從 Jive.com 要求所需的使用者安全性權杖。

下列螢幕擷取畫面顯示 Azure AD 中相關的對話方塊範例：

![設定使用者佈建](./media/active-directory-saas-jive-tutorial/IC698794.png "設定使用者佈建")

#### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 在 Azure 管理入口網站中的 [Jive] 應用程式整合頁面上，按一下 [設定使用者佈建] 以開啟 [設定使用者佈建] 對話方塊。
2. 在 [輸入您的 Jive 認證來啟用自動使用者佈建]  頁面上，提供以下組態設定：
   
    a. 在 [Jive 管理員使用者名稱] 文字方塊中，輸入在 Jive.com 已指派 **System Administrator** 設定檔的 Jive 帳戶名稱。
   
    b. 在 [Jive 管理員密碼]  文字方塊中，輸入這個帳戶的密碼。
   
    c. 在 [Jive 租用戶 URL]  文字方塊中，輸入 Jive 租用戶 URL。
      
      > [!NOTE]
      > Jive 租用戶 URL 是您的組織登入 Jive 所使用的 URL。  
      > 一般來說，該 URL 的格式如下：**www.\<organization\>.jive.com**。
      > 
      > 
   
    d. 按一下 [驗證]  來驗證您的組態。

    e. 按 [下一步] 按鈕以開啟 [確認] 頁面。

3. 在 [確認]  頁面上，按一下核取記號以儲存您的組態。

您現在可以建立測試帳戶，等待 10 分鐘，然後確認帳戶已同步至 Jive.com。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會將 Jive 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Jive，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Jive] 。
   
    ![設定單一登入](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您按一下存取面板中的 Jive 圖格時，您應該會自動登入您的 Jive 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png

