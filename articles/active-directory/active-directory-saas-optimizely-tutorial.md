---
title: "教學課程：Azure Active Directory 與 Optimizely 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Optimizely 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 2275daf25fd916932dbf54a7b069ecbfcf3e68cf
ms.lasthandoff: 12/08/2016


---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>教學課程：Azure Active Directory 與 Optimizely 整合
在本教學課程中，您會了解如何整合 Optimizely 與 Azure Active Directory (Azure AD)。

Optimizely 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Optimizely 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Optimizely (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Optimizely 與 Azure AD 的整合作業，需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 **Optimizely** 單一登入功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫中新增 Optimizely
2. 設定並測試 Azure AD 單一登入

## <a name="adding-optimizely-from-the-gallery"></a>從資源庫中新增 Optimizely
若要設定將 Optimizely 整合到 Azure AD 中，您需要從資源庫將 Optimizely 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Optimizely，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]

6. 在搜尋方塊中，輸入 **Optimizely**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. 在結果窗格中，選取 [Optimizely]，然後按一下 [完成]，以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Optimizely 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Optimizely 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Optimizely 中的相關使用者之間，建立連結關聯性。
建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值指定為 Optimizely 中 **Username** 的值。

若要設定及測試與 Optimizely 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Optimizely 測試使用者](#creating-an-optimizely-test-user)** - 在 Optimizely 中建立一個 Britta Simon 對應項目，該項目要與 Azure AD 中代表她的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 Optimizely 應用程式中設定單一登入。

Optimizely 應用程式預期 SAML 判斷提示會包含名為 "email" 的屬性。 "email" 的值應該是可通過 Azure AD 驗證的 Optimizely 可辨識電子郵件。 請設定此應用程式的 "email" 宣告。 您可以從應用程式的 [屬性]  索引標籤來管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。 

![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 

**若要設定與 Optimizely 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [Optimizely] 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。
   
    ![設定單一登入][5]

2. 在 [SAML Token 屬性] 對話方塊中，新增 "email" 屬性。
   
    a. 按一下 [加入使用者屬性] 來開啟 [加入使用者屬性] 對話方塊。 
   
    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)
   
    b. 在 [屬性名稱] 文字方塊中，輸入屬性名稱 "email"。
   
    c. 從 [屬性值] 清單中選取屬性值 "userprincipalname"，或任何包含 Azure AD 與 Optimizely 可辨識電子郵件的值。
   
    d. 按一下頁面底部的 [新增] 。

3. 按一下頂端功能表中的 [快速啟動] 。
   
    ![設定單一登入][6]

4. 在傳統入口網站的 [Optimizely] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][7] 

5. 在 [要如何讓使用者登入 Optimizely] 頁面上，選取 [Azure AD 單一登入]，然後按一下 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. 在 [設定 App 設定]  對話方塊頁面執行下列步驟： 
   
    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    a. 在 [登入 URL] 文字方塊中，輸入 `https://app.optimizely.net/contoso`。

    b.這是另一個 C# 主控台應用程式。 在 [識別碼] 文字方塊中，輸入：`urn:auth0:optimizely:contoso`。

    c. 按 [下一步] 。 

    > [!NOTE] 
    > [登入 URL] 和 [識別碼] 的值只是實際值的預留位置。 您可以稍後在本教學課程中找到從 Optimizely 取得實際值的指示。

1. 在 [設定在 Optimizely 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
   
    b. 複製 [單一登入服務 URL]。

2. 若要為您的應用程式設定 SSO，請連絡您的 Optimizely 帳戶管理員，並提供下列資訊：
   
   * 您所下載的憑證 
   * 單一登入服務 URL
     
     Optimizely 會回應您的電子郵件，提供單一登入 URL (SP 啟始的 SSO) 和識別碼 (服務提供者的實體識別碼) 的值。

3. 返回 [設定應用程式設定]  對話方塊頁面，然後執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)
   
    a. 在 [登入 URL] 文字方塊中，輸入Optimizely 所提供 [SP 啟始的 SSO URL]。
   
    b.這是另一個 C# 主控台應用程式。 在 [識別碼] 文字方塊中，輸入 Optimizely 所提供的**服務提供者實體識別碼**。
   
    c. 按 [下一步] 。

4. 在 [設定在 Optimizely 單一登入]  頁面上，執行下列步驟：
   
    ![Azure AD 單一登入][10]
   
    a. 選取單一登入設定確認。
   
    b. 按 [下一步] 。

5. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

6. 在不同的瀏覽器視窗中，登入您的 Optimizely 應用程式。

7. 按一下您在右上角的帳戶名稱，然後按 [帳戶設定] 。
   
    ![Azure AD 單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

8. 在 [帳戶] 索引標籤的 [概觀] 區段中，選取 [單一登入] 下的 [啟用 SSO] 方塊。
   
    ![Azure AD 單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。
在 [使用者] 清單中，選取 [Britta Simon] 。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。

6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。
   
    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-an-optimizely-test-user"></a>建立 Optimizely 測試使用者
在本節中，您會在 Optimizely 中建立名為 Britta Simon 的使用者。

1. 在首頁上，選取 Collaborators  \(共同作業者) 索引標籤

2. 按一下 New Collaborator  \(新增共同作業者)，來將新的共同作業者加入專案。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. 填寫電子郵件地址，並為其指派角色。 按一下 [邀請] 。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

1. 這些人員將會收到一封電子郵件邀請。 他們必須使用該電子郵件地址來登入 Optimizely。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會把 Optimizely 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Optimizely，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Optimizely] 。
   
    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 

4. 在 [所有使用者] 清單中，選取 [Britta Simon] 。

5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 Optimizely 磚時，應該會自動登入 Optimizely 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png

