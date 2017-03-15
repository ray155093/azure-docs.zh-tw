---
title: "教學課程：Azure Active Directory 與 Tableau Online 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Tableau Online 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: de8292e6d3642776fc684ded731f48845de846d4
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>教學課程：Azure Active Directory 與 Tableau Online 整合
在本教學課程中，您將了解如何整合 Tableau Online 與 Azure Active Directory (Azure AD)。

Tableau Online 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Tableau Online 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Tableau Online 單一登入 (SSO)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Tableau Online 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 **Tableau Online** SSO 的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD SSO。 

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Tableau Online
2. 設定並測試 Azure AD SSO

## <a name="adding-tableau-online-from-the-gallery"></a>從資源庫新增 Tableau Online
若要設定 Tableau Online 與 Azure AD 整合，您需要從資源庫將 Tableau Online 加入到受管理的 SaaS 應用程式清單中。

**如要從資源庫新增 Tableau Online，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Tableau Online**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_01.png)
7. 在結果窗格中，選取 [Tableau Online]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Tableau Online 搭配運作的 Azure AD SSO。

若要讓 SSO 運作，Azure AD 必須知道 Tableau Online 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Tableau Online 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值指派為 Tableau Online 中 **Username** 的值。

若要設定及測試與 Tableau Online 搭配運作的 Azure AD SSO，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Tableau Online 測試使用者](#creating-a-Tableau-Online-test-user)** - 使 Tableau Online 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-sso"></a>設定 Azure AD SSO
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD SSO，並在您的 Tableau Online 應用程式中設定 SSO。

**若要設定與 Tableau Online 搭配運作的 Azure AD SSO，請執行下列步驟：**

1. 按一下頂端功能表中的 [快速啟動] 。
   
    ![設定單一登入][6]
2. 在傳統入口網站的 [Tableau Online] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][7] 
3. 在 [您希望使用者如何登入 Tableau Online] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_06.png)
4. 在 [設定 App 設定]  對話方塊頁面執行下列步驟： 
   
    ![設定單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_07.png)
  1. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://sso.online.tableau.com`
  2. 按 [下一步] 。
5. 在 [設定在 Tableau Online 單一登入] 頁面上，按一下 [下載中繼資料]，然後將檔案儲存在您的電腦中。
   
    ![設定單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_08.png)
6. 選取單一登入設定確認，然後按一下 [下一步] 。
   
    ![Azure AD 單一登入][10]
7. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]
8. 在不同的瀏覽器視窗中，登入您的 Tableau Online 應用程式。 依序前往 [設定] 和 [驗證]。
   
    ![設定單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
9. 在 [驗證類型] 區段底下， 核取 [使用 SAML 的單一登入] 核取方塊以啟用 SAML。
   
    ![設定單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)
10. 向下捲動到 [將中繼資料檔匯入到 Tableau Online]  區段。  按一下 [瀏覽]，然後匯入您從 Azure AD 下載的中繼資料檔案。 然後，按一下 [套用] 。
   
   ![設定單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)
11. 在 [比對判斷提示]  區段中，針對電子郵件地址、名字和姓氏插入對應的識別提供者判斷提示名稱。 若要從 Azure AD 取得這項資訊︰ 
  1. 回到 Azure AD。 在 Azure 傳統入口網站中的 [Tableau Online] 應用程式整合頁面上。
  2. 在頂端的功能表上按一下 [屬性]。 
  3. 複製下列值的名稱：userprincipalname、givenname 和 surname。
   
     ![Azure AD 單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
  4. 切換至 Tableau Online 應用程式，然後將 [Tableau Online 屬性] 區段設定如下︰
     * 電子郵件︰**mail** 或 **userprincipalname**
     * 名字︰ **givenname**
     * 姓氏︰ **surname**
   
   ![設定單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_05.png) 
  1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
  2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
  3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_06.png) 
  1. 在 [名字] 文字方塊中，輸入 **Britta**。  
  2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
  3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
  4. 在 [角色] 清單中選取 [使用者]。 
  5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_08.png) 
  1. 記下 [新密碼] 的值。
  2. 按一下頁面底部的 [新增] 。   

### <a name="create-a-tableau-online-test-user"></a>建立 Tableau Online 測試使用者
在本節中，您要在 Tableau Online 中建立名為 Britta Simon 的使用者。

1. 在 [Tableau Online] 中，依序按一下 [設定] 和 [驗證] 區段。 向下捲動至 [選取使用者]  區段。 依序按一下 [新增使用者] 和 [輸入電子郵件地址]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. 選取 [新增單一登入 (SSO) 驗證的使用者] 。 在 [輸入電子郵件地址] 文字方塊中，新增 britta.simon@contoso.com
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. 按一下 [建立] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會把 Tableau Online 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**如要將 Britta Simon 指派給 Tableau Online，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Tableau Online] 。
   
    ![設定單一登入](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [所有使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD SSO 組態。

當您在存取面板中按一下 [Tableau Online ] 圖格時，應該會自動登入您的 Tableau Online 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_205.png

