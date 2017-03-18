---
title: "教學課程：Azure Active Directory 與 RunMyProcess 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 RunMyProcess 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 8aeac420b3848a270a3a9a325c9db0f1569a33e6
ms.openlocfilehash: 2f6bcdd9ea97494d3d12ea4ffd541b4b95a91c4f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>教學課程：Azure Active Directory 與 RunMyProcess 整合
本教學課程旨在說明如何整合 RunMyProcess 與 Azure Active Directory (Azure AD)。

RunMyProcess 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 RunMyProcess 的人員
* 您可以讓您的使用者使用他們的 Azure AD 帳戶自動登入 RunMyProcess 單一登入 (SSO)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 RunMyProcess 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 RunMyProcess SSO 的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD SSO。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 RunMyProcess
2. 設定並測試 Azure AD SSO

## <a name="add-runmyprocess-from-the-gallery"></a>從資源庫新增 RunMyProcess
若要設定將 RunMyProcess 整合到 Azure AD 中，您需要從資源庫將 RunMyProcess 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 RunMyProces，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **RunMyProcess**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. 在結果窗格中選取 [RunMyProcess]，然後按一下 [完成] 來新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試透過 RunMyProcess 使用 Azure AD 單一登入功能。

若要讓單一登入能夠運作，Azure AD 必須知道 RunMyProcess 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 RunMyProcess 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值指派為 RunMyProcess 中 **Username** 的值。

若要設定及測試與 RunMyProcess 搭配運作的 Azure AD SSO，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 RunMyProcess 測試使用者](#creating-a-runmyprocess-test-user)** - 在 RunMyProcess 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，並在您的 RunMyProcess 應用程式中設定 SSO。

**若要設定透過 RunMyProcess 使用 Azure AD SSO，請執行下列步驟：**

1. 在傳統入口網站的 [RunMyProcess] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 RunMyProcess] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
  1. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://live.runmyprocess.com/live/<tenant id>`。 
  2. 按一下 [下一步]。
    >[!NOTE]
    >請注意，您必須使用實際的「登入 URL」來更新這些值。 若要取得此值，請透過 <mailto:support@runmyprocess.com> 連絡 RunMyProcess 支援小組。
    >  
4. 在 [設定在 RunMyProcess 單一登入] 頁面上，按一下 [下載憑證]，然後將檔案儲存在您的電腦上：
   
    ![設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. 在不同的網頁瀏覽器視窗中，以管理員身分登入您的 RunMyProcess 租用戶。
6. 在左方瀏覽面板中，按一下 [帳戶]，然後選取 [組態]。
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. 移至 [驗證方法] 區段並執行下列步驟：
   
    ![在應用程式端設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
  1. 在 [方法]，選取 [使用 Samlv2 進行 SSO]。 
  2. 在 [SSO 重新導向] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [SAML SSO URL] 值。
  3. 在 [登出重新導向] 文字方塊中，輸入 Azure AD 應用程式組態精靈中 [單一登出服務 URL] 的值。
  4. 在 [名稱識別碼格式] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [名稱識別碼格式] 值。
  5. 複製所下載憑證檔案的內容，然後將它貼至 [憑證] 文字方塊中。 
  6. 按一下 [儲存]  圖示。
8. 在傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
9. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
 1. 針對 [使用者類型]，選取 [您組織中的新使用者]。  
 2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。 
 3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：

   ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png)  
 1. 在 [名字] 文字方塊中，輸入 **Britta**。   
 2. 在 [姓氏] 文字方塊中，輸入 **Simon**。 
 3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。 
 4. 在 [角色] 清單中選取 [使用者]。 
 5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png)  
 1. 記下 [新密碼] 的值。
 2. 按一下頁面底部的 [新增] 。   

### <a name="create-a-runmyprocess-test-user"></a>建立 RunMyProcess 測試使用者
若要讓 Azure AD 使用者可以登入 RunMyProcess，必須將他們佈建到 RunMyProcess。 RunMyProcess 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 RunMyProcess 公司網站。
2. 按一下 [帳戶] 並選取 [使用者]，然後按一下 [新增使用者]。
   
   ![新增使用者](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "新增使用者")
3. 在 [使用者設定]  區段中，執行下列步驟：
   
   ![設定檔](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "設定檔")   
 1. 在相關的文字方塊中，輸入您想要佈建之有效 AAD 帳戶的 [名稱] 與 [電子郵件]。 
 2. 選取 [IDE 語言]、[語言] 和 [設定檔]。 
 3. 選取 [傳送帳戶建立電子郵件給我] 。 
 4. 按一下 [儲存] 。
   
   >[!NOTE]
   >您可以使用任何其他的 RunMyProcess 使用者帳戶建立工具或 RunMyProcess 提供的 API 來佈建 Azure Active Directory 使用者帳戶。 
   > 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是授與 Britta Simon 對 RunMyProcess 的存取權，使她能夠使用 Azure SSO。

![指派使用者][200] 

**若要將 Britta Simon 指派給 RunMyProcess，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [RunMyProcess]。
   
    ![設定單一登入](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派]。
   
    ![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD SSO 組態。

當您在存取面板中按一下 RunMyProcess 圖格時，應該會自動登入您的 RunMyProcess 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png

