---
title: "教學課程：Azure Active Directory 與 Heroku 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Heroku 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: d30605e4757b484f327a784b73f939b62ef59373
ms.contentlocale: zh-tw
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>教學課程：Azure Active Directory 與 Heroku 整合

在本教學課程中，您會了解如何整合 Heroku 與 Azure Active Directory (Azure AD)。

Heroku 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Heroku 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Heroku (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 Heroku 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Heroku 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫加入 Heroku
2. 設定並測試 Azure AD 單一登入

## <a name="adding-heroku-from-the-gallery"></a>從資源庫加入 Heroku
若要設定 Heroku 與 Azure AD 整合，您需要從資源庫將 Heroku 加入受管理的 SaaS 應用程式清單中。

**若要從資源庫加入 Heroku，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中輸入 **Heroku**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_search.png)

5. 在結果窗格中，選取 [Heroku]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Heroku 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Heroku 與 Azure AD 中互相對應的使用者。 換句話說，必須在某位 Azure AD 使用者與 Heroku 中的相關使用者之間，建立連結關聯性。

在 Heroku 中，將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試對 Heroku 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Heroku 測試使用者](#creating-a-heroku-test-user)** - 在 Heroku 中建立一個 Britta Simon 對應項目，其要與 Azure AD 中代表使用者的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 Heroku 中設定單一登入。

**若要使用 Heroku 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Heroku] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_samlbase.png)

3. 在 [Heroku 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰    
    `https://sso.heroku.com/saml/<company-name>/init`

    b.這是另一個 C# 主控台應用程式。 在 [識別碼 URL] 文字方塊中，使用下列模式輸入 URL：            
    `https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    >這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 您可以從 Heroku 小組取得這些值，本文後面幾節會有相關說明。 
        
4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/active-directory-saas-heroku-tutorial/tutorial_general_400.png)

6. 若要在 Heroku 中啟用 SSO，請執行下列步驟：
   
    a. 以系統管理員身分登入 Heroku 帳戶。

    b. 按一下 [設定]  索引標籤。

    c. 按一下 [單一登入] 頁面的 [上傳中繼資料]。

    d. 上傳您從 Azure 入口網站下載的中繼資料檔案。

    e. 安裝成功後，系統管理員會看到確認對話方塊，以及供使用者使用的 SSO 登入 URL。 

    f. 複製 [Heroku 登入 URL] 和 [Heroku 實體識別碼] 值，返回 Azure 入口網站中的 [Heroku 網域和 URL] 區段，並將這些值分別貼到 [登入 URL] 和 [識別碼] 文字方塊。

    ![設定單一登入](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_52.png) 
    
8. 按一下 [下一步] 。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory 企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-heroku-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-heroku-tutorial/create_aaduser_02.png) 

3. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-heroku-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-heroku-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-a-heroku-test-user"></a>建立 Heroku 測試使用者

在本節中，您要在 Heroku 中建立名為 Britta Simon 的使用者。 Heroku 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 存取 Heroku 時，如果使用者還不存在，就會建立新使用者。 帳戶佈建之後，使用者會收到驗證電子郵件，而且必須按一下通知連結。

>[!NOTE]
>如果您需要手動建立使用者，您需要連絡 [Heroku 用戶端支援小組](https://www.heroku.com/support)。
>  

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Heroku 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Heroku，請執行以下步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Heroku] 。

    ![設定單一登入](./media/active-directory-saas-heroku-tutorial/tutorial_heroku_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Heroku] 圖格時，應該會自動登入您的 Heroku 應用程式。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-heroku-tutorial/tutorial_general_203.png

