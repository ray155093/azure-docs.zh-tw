---
title: "教學課程：Azure Active Directory 與 Evernote 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Evernote 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: e4e2ca40ae270e7d3bfe4e828fcd571b5594ffc8
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>教學課程：Azure Active Directory 與 Evernote 整合

在本教學課程中，您將了解如何整合 Evernote 與 Azure Active Directory (Azure AD)。

Evernote 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Evernote 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Evernote (單一登入)
- 您可以在 Azure 管理入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Evernote 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Evernote 單一登入的訂用帳戶


> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Evernote
2. 設定並測試 Azure AD 單一登入


## <a name="adding-evernote-from-the-gallery"></a>從資源庫新增 Evernote
若要設定 Evernote 與 Azure AD 的整合作業，您必須從資源庫將 Evernote 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Evernote，請執行下列步驟：**

1. 在 **[Azure 管理入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **Evernote**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search01.png)

5. 在結果窗格中，選取 [Evernote]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search_result01.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Evernote 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Evernote 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Evernote 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，是將 Azure AD 中 [使用者名稱] 的值，指派為 Evernote 中 [使用者名稱] 的值。

若要設定並測試透過 Evernote 使用 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
4. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 管理入口網站中啟用 Azure AD 單一登入，並在您的 Evernote 中設定單一登入。

**若要使用 Evernote 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 管理入口網站的 [Evernote] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，選取 [SAML 型登入] 做為 [模式]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. 在 [Evernote 網域和 URL] 區段中，如果您想要在 [IDP 起始模式] 中設定應用程式，則不需要執行任何步驟。

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_idp.png)
    
4. 在 [Evernote 網域和 URL] 區段中，如果您想要在 [SP 起始模式] 中設定應用程式，請執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sp_01.png)
    
    a. 按一下 [顯示進階 URL 設定] 選項

    b. 在 [登入 URL] 文字方塊中，輸入下列登入 URL：`https://www.evernote.com/Login.action`

5. 在 [SAML 簽署憑證] 區段中，按一下 [建立新憑證]。

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_cert.png)     

6. 在 [建立新憑證] 對話方塊中，按一下行事曆圖示並選取 [到期日]。 然後按一下 [儲存] 按鈕。

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_general_300.png)

7. 在 [SAML 簽署憑證] 區段中，選取 [啟用新憑證] 並按一下 [儲存] 按鈕。

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_makecertactive.png)

8. 在 [變換憑證] 快顯視窗上，按一下 [確定]。

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

9. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

10. 在 [Evernote 組態] 區段上，按一下 [設定 Evernote] 以開啟 [設定登入] 視窗。

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configuresignon.png)

11. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Evernote 公司網站。

12. 移至 [管理主控台]

    ![管理主控台](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

13. 從 [管理主控台]，移至 [安全性]，然後選取 [單一登入]

    ![SSO 設定](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

14. 設定下列值：

    a.  **啟用 SSO：**預設會啟用 SSO (按一下 [停用單一登入] 以移除 SSO 需求)

    b. **SAML HTTP 要求 URL** - 從 Azure AD 的 [設定 Evernote] 區段中，輸入 [SAML 單一登入服務 URL]

    c. **X.509 憑證** - 在記事本中開啟已從 Azure AD 下載的憑證並複製內容，包括 "BEGIN CERTIFICATE" 和 "END CERTIFICATE"

    ![憑證設定](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)

    d. 按一下 [儲存變更] 


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 管理入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png) 

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。 



### <a name="creating-an-evernote-test-user"></a>建立 Evernote 測試使用者

若要讓 Azure AD 使用者可以登入 Evernote，則必須將他們佈建到 Evernote。  
Evernote 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Evernote 公司網站。

2. 按一下 [管理主控台]。

    ![管理主控台](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. 從 [管理主控台]，移至 [新增使用者]。

    ![新增測試使用者](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. 在 [新增小組成員]的 [電子郵件] 文字方塊中，輸入使用者帳戶的電子郵件地址，然後按一下 [邀請]。

    ![新增測試使用者](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. 傳送邀請之後，Azure Active Directory 帳戶持有者會收到一封接受邀請的電子郵件。   


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Evernote 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Evernote，請執行以下步驟：**

1. 在 Azure 管理入口網站中，開啟應用程式檢視，然後瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Evernote]。

    ![設定單一登入](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    


### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Evernote 圖格時，應該會登入您的 Evernote 應用程式。 您將會以組織帳戶登入，但隨後需要以您的個人帳戶登入。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

