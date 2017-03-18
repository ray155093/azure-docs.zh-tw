---
title: "教學課程：Azure Active Directory 與 ADP GlobalView 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 ADP GlobalView 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0f767b96fa8f8f9d1a22709633bee64f0477ffb3
ms.openlocfilehash: 46fbe3233af22605d4cd89227b91313128e523f9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>教學課程：Azure Active Directory 與 ADP GlobalView 整合
本教學課程旨在說明如何整合 ADP GlobalView 與 Azure Active Directory (Azure AD)。  

ADP GlobalView 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 ADP GlobalView 的人員
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 ADP GlobalView 單一登入 (SSO)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 ADP GlobalView 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 ADP GlobalView SSO 的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD SSO。  

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 ADP GlobalView
2. 設定並測試 Azure AD SSO

## <a name="add-adp-globalview-from-the-gallery"></a>從資源庫新增 ADP GlobalView
若要設定將 ADP GlobalView 整合到 Azure AD 中，您需要從資源庫將 ADP GlobalView 加入受管理的 SaaS 應用程式清單。

**若要從資源庫新增 ADP GlobalView，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 [ADP GlobalView] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)
7. 在結果窗格中，選取 [ADP GlobalView]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 ADP GlobalView 搭配運作的 Azure AD SSO。

若要讓 SSO 運作，Azure AD 必須知道 ADP GlobalView 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 ADP GlobalView 中相關使用者之間的連結關聯性。  

建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值指派為 ADP GlobalView 中 **Username** 的值。

若要設定及測試與 ADP GlobalView 搭配運作的 Azure AD SSO，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 ADP GlobalView 測試使用者](#creating-a-adp-globalview-test-user)** - 在 ADP GlobalView 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD SSO，並在您的 ADP GlobalView 應用程式中設定 SSO。

ADP GlobalView 應用程式需要特定格式的 SAML 判斷提示，要求您加入自訂屬性對應到您的 SAML 權杖屬性組態。 

以下螢幕擷取畫面顯示上述的範例。 宣告名稱一律為 **"PersonImmutableID"** ，且值已對應至 ExtensionAttribute2，其中包含使用者的 EmployeeID。 這裡，從 Azure AD 到 ADP GlobalView 的使用者對應將在 EmployeeID 上進行，但您可以將這對應至同樣根據您應用程式設定的不同值。 

您可以與 ADP GlobalView 小組合作，使用正確的使用者識別碼，並將該值與 **"PersonImmutableID"** 宣告進行對應。 如圖所示，您也可以對應電子郵件和 UserID 宣告。

![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png) 

您必須先連絡您的 ADP GlobalView 支援小組，向其要求您租用戶的唯一識別碼屬性值，才能設定 SAML 判斷提示。 您需要此值來設定應用程式的自訂宣告。

**若要設定與 ADP GlobalView 搭配運作的 Azure AD SSO，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [ADP GlobalView] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 ADP GlobalView] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png) 
  1. 在 [識別碼] 文字方塊中，使用下列其中一個模式，輸入用來識別 ADP GlobalView 應用程式的 URL：`https://<server name>.globalview.adp.com/federate2` 或 `https://<server name>.globalview.adp.com/federate`
  2. 在 [回覆 URL] 文字方塊中，使用下列其中一個模式，輸入 Azure AD 用來回應 ADP GlobalView 應用程式的 URL：`https://<server name>.globalview.adp.com/federate2/sp/ACS.saml2` 或 `https://<server name>.globalview.adp.com/federate/sp/ACS.saml2`
  3. 按 [下一步] 。
4. 在 [設定在 ADP GlobalView 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)   
  1. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
  2. 按 [下一步] 。
5. 若要為您的應用程式設定 SSO，請連絡您的 ADP GlobalView 支援小組，並提供下列資訊： 
   
   * 下載的**憑證**
   * **實體識別碼**
   * **SAML SSO URL**
   * **單一登出服務 URL**

    >[!NOTE]
    >在 **ADP GlobalView** 小組設定執行個體之後，從它們取得 **RelayState** 值。 請依照下面所述的步驟進行設定。 進行這項設定之後，您可以測試整合。 因此，請注意，此應用程式整合若要能運作，這是重要的組態。
    >

6. 若要在 Azure AD 中設定 RelayState 值，請執行下列步驟：   
 1. 以系統管理員身分登入 [Azure 管理入口網站](https://portal.azure.com)。
 2. 在左導覽窗格中，按一下 [更多服務]。 
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_07.png)  
 3. 在 [搜尋] 文字方塊中，輸入 **Azure Active Directory**，然後按一下相關的連結。
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_08.png)  
 4. 按一下 [企業應用程式]。
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_09.png) 
 5. 在 [管理] 區段中，按一下 [所有應用程式]。
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_10.png) 
 6. 在 [搜尋] 文字方塊中，輸入 **ADP eTime**，然後按一下相關的連結。 
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_11.png) 
 7. 在 [管理] 區段中，按一下 [單一登入]。
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_12.png)  
 8. 選取 [顯示進階 URL 設定]。
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_13.png)
 9. 在 [轉送狀態] 文字方塊中，輸入使用下列模式的值：
   
    `https://<server name>.globalview.adp.com/gvolution/session/<instance name>/sso` 
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_14.png)  
 10. 儲存設定。
7. 在 Azure 傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
8. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 傳統入口網站中建立一個名為 Britta Simon 的測試使用者。  

* 在 [使用者] 清單中，選取 [Britta Simon] 。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)  
 1. 針對 [使用者類型]，選取 [您組織中的新使用者]。  
 2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。 
 3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)  
 1. 在 [名字] 文字方塊中，輸入 **Britta**。    
 2. 在 [姓氏] 文字方塊中，輸入 **Simon**。 
 3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。 
 4. 在 [角色] 清單中選取 [使用者]。 
 5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)  
 1. 記下 [新密碼] 的值。  
 2. 按一下頁面底部的 [新增] 。   

### <a name="create-a-adp-globalview-test-user"></a>建立 ADP GlobalView 測試使用者
本節目標是在 ADP GlobalView 中建立名為 Britta Simon 的使用者。 請與 ADP GlobalView 支援小組合作，在 ADP GlobalView 帳戶中加入使用者。 

>[!NOTE]
>如果您需要手動建立使用者，您需要連絡 ADP GlobalView 支援小組。
> 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是授與 Britta Simon 對 ADP GlobalView 的存取權，使她能夠使用 Azure SSO。

![指派使用者][200] 

**若要將 Britta Simon 指派給 ADP GlobalView，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [ADP GlobalView] 。
   
    ![設定單一登入](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD SSO 組態。  

當您在存取面板中按一下 [ADP GlobalView] 圖格時，應該會自動登入您的 ADP GlobalView 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

