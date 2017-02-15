---
title: "教學課程：Azure Active Directory 與 SAP Cloud for Customer 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 SAP Cloud for Customer 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: bec672005b0b1856faeb230f5674013a37add774


---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>教學課程：Azure Active Directory 與 SAP Cloud for Customer 整合
在本教學課程中，您會了解如何整合 SAP Cloud for Customer 與 Azure Active Directory (Azure AD)。

SAP Cloud for Customer 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 SAP Cloud for Customer 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SAP Cloud for Customer (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 SAP Cloud for Customer 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 SAP Cloud for Customer 單一登入功能的訂用帳戶

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

1. 從資源庫新增 SAP Cloud for Customer
2. 設定並測試 Azure AD 單一登入

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>從資源庫新增 SAP Cloud for Customer
若要設定 SAP Cloud for Customer 與 Azure AD 整合，您需要從資源庫將 SAP Cloud for Customer 加入到受管理的 SaaS 應用程式清單中。

**若要從資源庫加入 SAP Cloud for Customer，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]

6. 在搜尋方塊中輸入 **SAP Cloud for Customer**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_01.png)

7. 在結果窗格中，選取 [SAP Cloud for Customer]，然後按一下 [完成] 來新增應用程式。
   
    ![Active Directory](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 SAP Cloud for Customer 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 SAP Cloud for Customer 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 SAP Cloud for Customer 中的相關使用者之間，建立連結關聯性。

若要使用 SAP Cloud for Customer 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 SAP Cloud for Customer 測試使用者](#creating-an-sap-business-bydesign-test-user)** - 使 SAP Cloud for Customer 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，並在 SAP Cloud for Customer 應用程式中設定單一登入。 

**若要使用 SAP Cloud for Customer 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [SAP Cloud for Customer] 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_80.png) 

2. 在屬性 SAML Token 屬性清單中，選取 nameidentifier 屬性，然後按一下 [編輯] 。
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_84.png) 

3. 在 [編輯使用者屬性]  對話方塊上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_85.png) 

    a. 從 [屬性值] 清單中，選取 **ExtractMailPrefix()** 函式。

    b. 從 [郵件] 清單中，選取您想要用於實作的使用者屬性。 
    例如，如果您想要使用 EmployeeID 為唯一的使用者識別碼，而且已在 ExtensionAttribute2 中儲存屬性值，則選取 [user.extensionattribute2]。 

    c. 按一下頁面底部的 [新增] 。 


1. 在傳統入口網站的 [SAP Cloud for Customer] 應用程式整合頁面上，按一下 [設定單一登入]。
   
    ![設定單一登入][6] 

2. 在 [要如何讓使用者登入 SAP Cloud for Customer] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_03.png) 

3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_04.png) 
   
    a. 在 [登入 URL] 文字方塊中，使用以下模式輸入使用者用來登入 SAP Cloud for Customer 應用程式的 URL：`https://<server name>.crm.ondemand.com`
   
    b. 按 [下一步] 

4. 在 [設定在 SAP Cloud for Customer 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_05.png)
   
    a. 按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。
   
    b. 按 [下一步] 。

5. 為了設定 SSO，請執行下列步驟：
   
    a. 使用系統管理員權限登入 SAP Cloud for Customer 入口網站。
   
    b. 瀏覽至 [應用程式和使用者管理常見工作]，然後按一下 [識別提供者] 索引標籤。
   
    c. 按一下 [新增識別提供者]，然後選取您從 Azure 傳統入口網站下載的中繼資料 XML 檔案。 藉由匯入中繼資料，系統會自動上傳所需的簽章憑證和加密憑證。
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure AD 要求 SAML 要求中必須有「判斷提示取用者服務 URL」元素，因此請選取 [包括判斷提示取用者服務 URL] 核取方塊。
   
    e. 按一下 [啟用單一登入]。
   
    f. 儲存您的變更。
   
    g. 按一下 [我的系統] 索引標籤。
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. 複製 **SSO URL** 並將它貼到 [Azure AD 登入 URL] 文字方塊。
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. 透過選取 [手動選取識別提供者]，指定員工是否可以在以使用者識別碼和密碼登入或 SSO 之間進行手動選擇。
   
    j. 在 [SSO URL] 區段中，指定您的員工應用來登入系統的 URL。 
    在 [傳送給員工的 URL] 清單中，您可以在下列選項之間做選擇︰
   
    **非 SSO URL**
   
    系統只會傳送一般系統 URL 給員工。 員工無法使用 SSO 來登入，而是必須改為使用密碼或憑證。
   
    **SSO URL** 
   
    系統只會傳送 SSO URL 給員工。 員工可以使用 SSO 來登入。 驗證要求會透過 IdP 重新導向。
   
    **自動選取**
   
    如果未啟用 SSO，系統會傳送一般系統 URL 給員工。 如果已啟用 SSO，系統會檢查員工是否有密碼。 如果有密碼，便會將 SSO URL 和非 SSO URL 傳送給員工。 但如果員工沒有密碼，則只會傳送 SSO URL 給員工。
   
    k. 儲存您的變更。

6. 在傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]

7. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在 Azure 傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_09.png) 

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。

6. 在 [使用者設定檔] 對話方塊頁面上，執行下列步驟： ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_06.png) 
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。
   
    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapcloudforcustomer-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-an-sap-cloud-for-customer-test-user"></a>建立 SAP Cloud for Customer 測試使用者
在本節中，您要在 SAP Cloud for Customer 中建立名為 Britta Simon 的使用者。 請與 SAP Cloud for Customer 支援小組合作，以在 SAP Cloud for Customer 平台中新增使用者。 

> [!NOTE]
> 請確定 NameID 值符合 SAP Cloud for Customer 平台中的使用者名稱欄位。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會將 SAP Cloud for Customer 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 SAP Cloud for Customer，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 

2. 在應用程式清單中，選取 [SAP Cloud for Customer] 。
   
    ![設定單一登入](./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_sapcloudforcustomer_50.png) 

3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]

4. 在 [使用者] 清單中，選取 [Britta Simon] 。

5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

在存取面板中按一下 [SAP Cloud for Customer] 圖格，應該會自動登入您的 SAP Cloud for Customer 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapcloudforcustomer-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO2-->


