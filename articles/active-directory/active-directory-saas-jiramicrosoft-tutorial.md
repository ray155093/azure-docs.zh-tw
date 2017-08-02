---
title: "教學課程：Azure Active Directory 與 JIRA SAML SSO by Microsoft 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 JIRA SAML SSO by Microsoft 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0dc847b9-eec4-4c31-845e-0144ddedc4a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: b5f7813c8244d2964b6894ae49cd64e0ee71b704
ms.contentlocale: zh-tw
ms.lasthandoff: 07/19/2017

---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>教學課程：Azure Active Directory 與 JIRA SAML SSO by Microsoft 整合

在本教學課程中，您會了解如何整合 JIRA SAML SSO by Microsoft 與 Azure Active Directory (Azure AD)。

JIRA SAML SSO by Microsoft 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 JIRA SAML SSO by Microsoft 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 JIRA SAML SSO by Microsoft (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 JIRA SAML SSO by Microsoft 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 安裝在 Windows 64 位元伺服器 (在內部部署或雲端 IaaS 基礎結構上) 的 JIRA 伺服器應用程式
- JIRA 伺服器已啟用 HTTPS
- 請注意下一節提及支援的 JIRA 外掛程式版本。
- JIRA 伺服器可從網際網路連上，特別是連線至 Azure AD 登入頁面來進行驗證，且應該能夠接收來自 Azure AD 的權杖
- JIRA 中已設定管理員認證
- JIRA 中已停用 WebSudo
- JIRA 伺服器應用程式中建立的測試使用者

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用 JIRA 的生產環境。 先在應用程式的開發或預備環境中測試整合，然後使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在這裡取得一個月試用：[試用優惠](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="supported-versions-of-jira"></a>支援的 JIRA 版本 

目前支援下列 JIRA 版本：

- JIRA 核心和軟體：6.0 至 7.2.0
- JIRA 服務台：3.0 至 3.2

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 JIRA SAML SSO by Microsoft
2. 設定並測試 Azure AD 單一登入

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>從資源庫新增 JIRA SAML SSO by Microsoft
如要設定將 JIRA SAML SSO by Microsoft 整合到 Azure AD 中，您需要從資源庫將 JIRA SAML SSO by Microsoft 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 JIRA SAML SSO by Microsoft，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **JIRA SAML SSO by Microsoft**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_search.png)

5. 在結果面板中，選取 [JIRA SAML SSO by Microsoft]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 JIRA SAML SSO by Microsoft 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 JIRA SAML SSO by Microsoft 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 JIRA SAML SSO by Microsoft 中的相關使用者之間，建立連結關聯性。

在 JIRA SAML SSO by Microsoft 中，將 Azure AD 中**使用者名稱**的值指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 JIRA SAML SSO by Microsoft 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 JIRA SAML SSO by Microsoft 測試使用者](#creating-a-jira-saml-sso-by-microsoft-test-user)** - 使 JIRA SAML SSO by Microsoft 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 JIRA SAML SSO by Microsoft 應用程式中設定單一登入。

**若要設定與 JIRA SAML SSO by Microsoft 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [JIRA SAML SSO by Microsoft] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_samlbase.png)

3. 在 [JIRA SAML SSO by Microsoft 網域及 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://<domain:port>/plugins/servlet/saml/auth`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://<domain:port>/`

    c. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的識別碼、回覆 URL 和登入 URL 來更新這些值。 如果連接埠是具名 URL，則為選擇性。 在設定 Jira 外掛程式 (本教學課程稍後會說明) 期間會收到這些值。
 
4. 若要產生**中繼資料** URL，執行下列步驟︰

    a. 按一下 [應用程式註冊]。
    
    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/appregistrations.png)
   
    b.這是另一個 C# 主控台應用程式。 按一下 [端點] 以開啟 [端點] 對話方塊。  
    
    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/endpointicon.png)

    c. 按一下複製按鈕複製 [同盟中繼資料文件] URL，並將它貼到 [記事本]。
    
    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/endpoint.png)
     
    d. 現在，移至 [JIRA SAML SSO by Microsoft] 的屬性頁面，使用 [複製] 按鈕複製 [應用程式識別碼]，並將它貼到 [記事本]。
 
    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/appid.png)

    e. 使用下列模式產生**中繼資料 URL**：`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`，並將此值複製到 [記事本]，稍後會用來設定外掛程式。

5. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_400.png)

6. 請連絡 [Microsoft](mailto:waadpartners@microsoft.com) 並提供下列 JIRA 外掛程式資訊。
    
    *   客戶名稱：
    *   主要網域名稱：
    *   Azure AD Premium：是/否 (外掛程式適用於所有客戶：免費、基本和進階 SKU)
    *   將會使用這項整合的使用者數目：
    *   JIRA 版本：
    *   註解：

7. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 JIRA 執行個體。

8. 將滑鼠停留在 cog 上，然後按一下 [附加元件]。
    
    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/addon1.png)

9. 在 [附加元件] 索引標籤區段下，按一下 [管理附加元件]。

    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/addon7.png)

10. 手動上傳 Microsoft 所提供的外掛程式。 安裝外掛程式之後，它會出現在 [管理附加元件] 區段的 [使用者安裝的附加元件] 區段中。

11. 按一下 [設定] 來設定新的外掛程式。

12. 在設定頁面上執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/addon5.png)
 
    a. 在 [中繼資料 URL] 中，貼上從 Azure AD 產生的**中繼資料 URL**，然後按一下 [解析] 按鈕。 這樣會讀取 IdP 中繼資料 URL 並填入所有欄位資訊。

    > [!Note]
    > 預設 SAML 使用者識別碼位置是名稱識別碼。 您可以將它變更為屬性選項，並輸入適當的屬性名稱。

    > [!TIP]
    > 請確定只有一個對應至應用程式的憑證，解析中繼資料時就不會發生錯誤。 如果有多個憑證，則解析中繼資料時，管理員會遇到錯誤。
    
    b.這是另一個 C# 主控台應用程式。 複製 [識別碼]、[回覆 URL] 和 [登入 URL] 值，然後在 Azure 入口網站的 [JIRA SAML SSO by Microsoft 網域及 URL] 中，分別貼到 [識別碼]、[回覆 URL] 和 [登入 URL] 文字方塊。

    c. 在 [登入按鈕名稱] 中，輸入您的組織要讓使用者在登入畫面上看到的按鈕名稱。

    d. 在 [SAML 使用者識別碼位置] 中，選取 [使用者識別碼在 Subject 陳述式的 NameIdentifier 元素中] 或 [使用者識別碼在 Attribute 元素中]。  此識別碼必須為 JIRA 使用者識別碼。 如果使用者識別碼不符，系統將不會允許使用者登入。 
    
    e. 如果您選取 [使用者識別碼在 Attribute 元素中] 選項，請在 [屬性名稱] 文字方塊中，輸入需要使用者識別碼的屬性名稱。 

    f. 如果您使用同盟網域 (例如 ADFS 等) 搭配 Azure AD，請按一下 [啟用主領域探索] 選項，並設定 [網域名稱]。
    
    g. 在 [網域名稱] 中，如果是使用以 ADFS 為基礎的登入，請在此輸入網域名稱。

    h. 如果您想要在使用者登出 JIRA 時登出 Azure AD，請勾選 [啟用單一登出]。 

    i. 按一下 [儲存] 按鈕以儲存設定。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jiramicrosoft-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jiramicrosoft-tutorial/create_aaduser_02.png) 

3. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jiramicrosoft-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-jiramicrosoft-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-a-jira-saml-sso-by-microsoft-test-user"></a>建立 JIRA SAML SSO by Microsoft 測試使用者

若要讓 Azure AD 使用者能夠登入 JIRA 內部部署伺服器，必須將他們佈建到 JIRA SAML SSO by Microsoft。 對於 JIRA SAML SSO by Microsoft，佈建是手動工作。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 JIRA 內部部署伺服器。

2. 將滑鼠停留在 cog 上，然後按一下 [使用者管理]。

    ![新增員工](./media/active-directory-saas-jiramicrosoft-tutorial/user1.png) 

3. 系統會將您重新導向至 [系統管理員存取] 頁面，以輸入**密碼**，然後按一下 [確認] 按鈕。

    ![新增員工](./media/active-directory-saas-jiramicrosoft-tutorial/user2.png) 

4. 在 [使用者管理] 索引標籤區段下，按一下 [建立使用者]。

    ![新增員工](./media/active-directory-saas-jiramicrosoft-tutorial/user3.png) 

5. 在 [建立新的使用者] 對話方塊中，執行下列步驟：

    ![新增員工](./media/active-directory-saas-jiramicrosoft-tutorial/user4.png) 

    a. 在 [電子郵件地址] 文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件地址。

    b.這是另一個 C# 主控台應用程式。 在 [全名] 文字方塊中，輸入像是 Britta Simon 的使用者全名。

    c. 在 [使用者名稱] 文字方塊中，輸入像是 Brittasimon@contoso.com 的使用者電子郵件。

    d. 在 [密碼] 文字方塊中，輸入使用者的密碼。

    e. 按一下 [建立使用者]。   

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 JIRA SAML SSO by Microsoft 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 JIRA SAML SSO by Microsoft，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [JIRA SAML SSO by Microsoft]。

    ![設定單一登入](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [JIRA SAML SSO by Microsoft] 圖格時，應該會自動登入您的 JIRA SAML SSO by Microsoft 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_203.png


