---
title: "教學課程：Azure Active Directory 與 Tableau Server 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Tableau Server 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0849c77c66ac8617e217a69696b5c404be3b5eb4
ms.openlocfilehash: 3aefb8188880fac5c1fcbe237a1e133584089e6a
ms.lasthandoff: 02/03/2017

---

# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>教學課程：Azure Active Directory 與 Tableau Server 整合
本教學課程旨在說明如何整合 Tableau Server 與 Azure Active Directory (Azure AD)。

Tableau Server 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Tableau Server 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Tableau Server (單一登入)
* 您可以使用 Azure 傳統入口網站集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Tableau Server 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 Tableau Server 單一登入 (SSO) 的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。 

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Tableau Server
2. 設定並測試 Azure AD 單一登入

## <a name="adding-tableau-server-from-the-gallery"></a>從資源庫新增 Tableau Server
若要設定 Tableau Server 與 Azure AD 整合，您需要從資源庫將 Tableau Server 加入到受管理的 SaaS 應用程式清單中。

**如要從資源庫新增 Tableau Server，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Tableau Server**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)
7. 在結果窗格中，選取 [Tableau Server]，然後按一下 [完成] 以加入應用程式。
   
    ![選取資源庫中的應用程式](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Tableau Server 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Tableau Server 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Tableau Server 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指派為 Tableau Server 中 **Username** 的值。

若要使用 Tableau Server 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Tableau Server 測試使用者](#creating-a-tableauserver-test-user)** - 使 Tableau Server 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節目標是能在 Azure 傳統入口網站啟用 Azure AD 單一登入，並在您的 Tableau Server 應用程式中設定單一登入。

Tableau Server 應用程式需要特定格式的 SAML 判斷提示。 以下螢幕擷取畫面顯示上述的範例。 

![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png) 

**若要設定透過 Tableau Server 使用 Azure AD 單一登入功能，請執行下列步驟：**

1. 在 Azure 傳統入口網站中的 [Tableau Server] 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。
   
    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png) 
2. 在 [SAML Token 屬性]  對話方塊中，執行下列步驟：

   1. 按一下 [新增使用者屬性] 來開啟 [新增使用者屬性] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png) 
   2. 在 [屬性名稱] 文字方塊中，輸入 **username**。
   3. 從 [屬性值] 清單中，選取 [user.displayname]。
   4. 按一下 [完成]。    

3. 按一下頂端功能表中的 [快速啟動] 。
   
    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)  
4. 按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
5. 在 [您希望使用者如何登入 Tableau Server] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png) 
6. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟，然後按 [下一步]：
   
    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png) 

   1. 在 [登入 URL] 文字方塊中，輸入 Tableau Server 的 URL。 
   2. 複製 [識別碼] 方塊中的 URL。
   3. 按 [下一步] 。
h
7. 在 [設定在 Tableau Server 單一登入] 頁面上，執行下列步驟，然後按 [下一步]：
   
    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png) 

   1. 按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。
   2. 按 [下一步] 。

8. 若要取得為應用程式設定的 SSO，您必須以系統管理員身分登入 Tableau Server 租用戶。
   
   1. 在 Tableau Server 組態中，按一下 [SAML] 索引標籤。
  
    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
   2. 選取 [使用 SAML 進行單一登入] 的核取方塊。
   3. 尋找從 Azure 傳統入口網站下載的同盟中繼資料檔案，然後將它上傳至 **SAML Idp 中繼資料檔案**。
   4. Tableau Server 傳回 URL—Tableau Server 使用者將存取的 URL，例如 http://tableau_server。 不建議使用 http://localhost。 不支援使用包含結尾斜線的 URL (例如，http://tableau_server/)。 複製 **Tableau Server 傳回 URL** 並將它貼至 Azure AD [單一登入 URL] 文字方塊，如步驟 3 所示
   5. SAML 實體識別碼—實體識別碼可唯一識別安裝至 IdP 的 Tableau Server。 您可以依意願再次在這裡輸入 Tableau Server URL，但是它不一定是您的 Tableau Server URL。 複製 **SAML 實體識別碼**並將它貼至 Azure AD [識別碼] 文字方塊，如步驟 3 所示。
   6. 按一下 [匯出中繼資料檔案]，並在文字編輯器應用程式中將它開啟。 利用 Http Post 與索引 0 找出判斷提示取用者服務 URL，並複製 URL。 現在將它貼至 Azure AD [回覆 URL] 文字方塊，如步驟 3 所示。 
   7. 按一下 [Tableau Server 組態] 頁面中的 [確定] 按鈕。
   
    >[!NOTE] 
    >如果您需要在 Tableau Server 上設定 SAML 的協助，請參閱[設定 SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm)。
    >

9. 在 Azure 傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
10. 在 [單一登入確認] 頁面上，按一下 [完成]。 
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

* 在 [使用者] 清單中，選取 [Britta Simon] 。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png) 
   
   1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png) 
   
   1. 在 [名字] 文字方塊中，輸入 **Britta**。  
   2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   4. 在 [角色] 清單中選取 [使用者]。
   5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png) 
   
   1. 記下 [新密碼] 的值。
   2. 按一下 [完成]。   

### <a name="creating-a-tableau-server-test-user"></a>建立 Tableau Server 測試使用者
本節目標是在 Tableau Server 中建立名為 Britta Simon 的使用者。 您必須在 Tableau Server 中佈建所有使用者。 也請注意，使用者的使用者名稱應符合您在 Azure AD 自訂屬性 **username**中設定的值。 透過正確的對應，應該可以有效整合， [設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)。

> [!NOTE]
> 如果您需要以手動方式建立使用者，您必須連絡貴組織的 Tableau Server 系統管理員。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節目標是授與 Britta Simon 對 Tableau Server 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**如要將 Britta Simon 指派給 Tableau Server，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Tableau Server] 。
   
    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。

![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 [Tableau Server] 圖格時，應該會自動登入您的 Tableau Server 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png

