---
title: "教學課程：Azure Active Directory 與 Boomi 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Boomi 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a60db808388bf1fdf9441518920f2eb4e5fcc4d
ms.openlocfilehash: bafbfb710d8cdb370d21d9299c447a0dfed3c468


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>教學課程：Azure Active Directory 與 Boomi 整合

在本教學課程中，您會了解如何整合 Boomi 與 Azure Active Directory (Azure AD)。

Boomi 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Boomi 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Boomi (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Boomi 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Boomi 單一登入的訂用帳戶


> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二項主要的基本工作組成：

1. 從資源庫新增 Boomi
2. 設定並測試 Azure AD 單一登入


## <a name="adding-boomi-from-the-gallery"></a>從資源庫新增 Boomi
若要設定將 Boomi 整合至 Azure AD 中，您需要從資源庫將 Boomi 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Boomi，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 

    ![Active Directory][1]

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![應用程式][2]

4. 按一下頁面底部的 [新增]。

    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。

    ![應用程式][4]

6. 在搜尋方塊中，輸入 **Boomi**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

7. 在結果窗格中，選取 [Boomi]，然後按一下 [完成] 以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Boomi 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Boomi 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Boomi 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值，指派為 Boomi 中 [Username] 的值。

若要設定及測試與 Boomi 搭配運作的 Azure AD 單一登入，您需要完成下列基本工作：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Boomi 測試使用者](#creating-a-boomi-test-user)** - 讓 Britta Simon 在 Boomi 中有一個對應項目連結至她的 Azure AD 代表身分。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

本節的目標是在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 Boomi 應用程式中設定單一登入。

Boomi 應用程式需要特定格式的 SAML 判斷提示，您必須以使用者的同盟識別碼來設定 NameIdentifier 屬性值。 根據預設，Azure AD 會使用 UserPrincipalName 作為 NameIdentifier 屬性。 但在 Boomi 中，您需要調整此值以符合使用者的同盟識別碼，才能成功整合。 您可以從 [屬性] 索引標籤中變更此值，如以下螢幕擷取畫面所示。 在完成正確對應後，整合才有作用。

![設定單一登入](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

**若要設定與 Boomi 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在傳統入口網站的 [Boomi] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入][6] 

2. 在 [要如何讓使用者登入 Boomi] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
 
    ![設定單一登入](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

3. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    a. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://platform.boomi.com/sso/<account name>/saml`

    b. 按 [下一步]。

    > [!NOTE] 
    > 請注意，這不是真正的值。 您必須使用實際的「回覆 URL」來更新此值。 請連絡 Boomi 支援小組以取得此值。

4. 於 [在 Boomi 設定單一登入] 頁面上，按 [下載憑證]，然後將檔案儲存在您的電腦上：

    ![設定單一登入](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png) 

    > [!NOTE]
    > 請注意，回應中的 NameID 宣告值必須符合 Boomi 系統中設定的同盟識別碼。 因此，請與 Boomi 支援小組合作，將您組織中適當的使用者識別碼對應為同盟識別碼。 Azure AD 預設會將 NameIdentifier 設為 UPN 值。 您可以從 [屬性] 索引標籤中變更此值，如以下螢幕擷取畫面所示。 在完成正確對應後，整合才有作用。 
    
    ![設定單一登入](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Boomi 公司網站。 

6. 瀏覽至 [公司名稱]，並移至 [設定]。

7. 按一下 [SSO 選項] 索引標籤並執行下列步驟。

    ![在應用程式端設定單一登入](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)

    a. 選取 [啟用 SAML 單一登入] 核取方塊。

    b. 按一下 [匯入]，將已從 Azure AD 下載的憑證上傳至**識別提供者憑證**。
    
    c. 在 [識別提供者登入 URL] 文字方塊中，輸入取自 Azure AD 應用程式組態精靈中的 [遠端登入 URL]。

    d. 針對 [同盟識別碼位置]，選取 [同盟識別碼位於主旨的 NameID 項目中] 選項按鈕。 

    e. 按一下 [儲存] 按鈕。

8. 在傳統入口網站中，選取單一登入設定確認，然後按 [下一步]。

    ![Azure AD 單一登入][10]

9. 在 [單一登入確認] 頁面上，按一下 [完成]。  
  
    ![Azure AD 單一登入][11]


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/create_aaduser_09.png) 

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/create_aaduser_05.png) 

    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c. 按 [下一步]。

6.  在 [使用者設定檔] 對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/create_aaduser_06.png) 

    a. 在 [名字] 文字方塊中，輸入 **Britta**。  

    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。

    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d. 在 [角色] 清單中選取 [使用者]。

    e. 按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-boomi-tutorial/create_aaduser_08.png) 

    a. 記下 [新密碼] 的值。

    b. 按一下 [完成]。   



### <a name="creating-a-boomi-test-user"></a>建立 Boomi 測試使用者

若要讓 Azure AD 使用者可以登入 Boomi，必須將他們佈建到 Boomi。 Boomi 需以手動方式佈建。

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>若要佈建使用者帳戶，請執行下列步驟：

1. 以系統管理員身分登入您的 Boomi 公司網站。

2. 登入之後，瀏覽至 [使用者管理]，然後移至 [使用者]。

    ![使用者](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Users")

3. 按一下 **+** 圖示，[新增/維護使用者角色] 對話方塊隨即開啟。

    ![使用者](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Users")

    ![使用者](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Users")

4. 輸入使用者的 [使用者電子郵件地址]。

5. 輸入使用者的 [名字] 和 [姓氏]。

6. 輸入使用者的 [同盟識別碼]。 每位使用者必須有同盟識別碼，以唯一地識別帳戶內的使用者。 

7. 指派 [標準使用者] 角色給使用者。 請勿指派系統管理員角色，因為這樣他會取得正常 Atmosphere 存取權和單一登入存取權。

8. 按一下 [確定]。

    > [!NOTE]
    > 使用者的密碼是透過識別提供者來管理，他不會收到附上 AtomSphere 帳戶登入密碼的歡迎通知電子郵件。 您可以使用任何其他的 Boomi 使用者帳戶建立工具或 Boomi 提供的 API 來佈建 AAD 使用者帳戶。 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Boomi 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派至 Boomi，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Boomi]。

    ![設定單一登入](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. 在頂端的功能表中，按一下 [使用者]。

    ![指派使用者][203] 

4. 在 [使用者] 清單中，選取 [Britta Simon]。

5. 在底部的工具列中，按一下 [指派]。
    
    ![指派使用者][205]



### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用「存取面板」來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [Boomi] 圖格時，應該會自動登入您的 Boomi 應用程式。


## <a name="additional-resources"></a>其他資源

* [關於如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


