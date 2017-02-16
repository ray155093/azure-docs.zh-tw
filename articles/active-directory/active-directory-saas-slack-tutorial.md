---
title: "教學課程：Azure Active Directory 與 Slack 整合 | Microsoft Docs"
description: "了解如何使用 Slack 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c7a0b761-75b7-4e6b-9980-71d645643a78
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4d5144c6a690c2338dec45b27dcb308328b6fecb
ms.openlocfilehash: 37440a8ba397c4dc227a448dfa574cebd14be49c


---

# <a name="tutorial-azure-active-directory-integration-with-slack"></a>教學課程：Azure Active Directory 與 Slack 整合

本教學課程旨在說明如何整合 Slack 與 Azure Active Directory (Azure AD)。

Slack 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Slack
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Slack (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 Slack 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Slack 單一登入功能的訂用帳戶


> [!NOTE]
>  若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Slack
2. 設定並測試 Azure AD 單一登入


## <a name="adding-slack-from-the-gallery"></a>從資源庫新增 Slack
若要設定將 Slack 整合到 Azure AD 中，您需要從資源庫將 Slack 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Slack，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 

    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
    
    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。
    
    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。

    ![應用程式][4]

6. 在 [搜尋方塊] 中，輸入 **Slack**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

7. 在結果窗格中，選取 [Slack]，然後按一下 [完成] 加入應用程式。

    ![選取資源庫中的應用程式](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試對 Slack 的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Slack 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Slack 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 Slack 中 **Username** 的值。

若要設定及測試與 Slack 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Slack 測試使用者](#creating-a-slack-test-user)** - 在 Slack 中建立一個與 Azure AD 中代表 Britta Simon 的項目連結的 Britta Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

本節的目標，是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 Slack 應用程式中設定單一登入。

Slack 應用程式需要特定格式的 SAML 判斷提示。 請設定此應用程式的下列宣告。 您可以從應用程式的 [屬性]索引標籤來管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。 

![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

**若要使用 Slack 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站中的 **Slack** 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

2. 在 [SAML Token 屬性]  對話方塊上，針對下表中顯示的每一列執行下列步驟：

    | 屬性名稱 | 屬性值 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. 按一下 [新增使用者屬性] 來開啟 [新增使用者屬性] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    
    b. 在 [屬性名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。
    
    c. 在 [屬性值] 清單中，輸入該資料列所顯示的屬性名稱。
    
    d. 按一下 [完成]

3. 在頂端的功能表中，按一下 [快速啟動] 。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png) 

4. 在 [要如何讓使用者登入 Slack] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
    
    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)

5. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟，然後按 [下一步]：

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<company name>.slack.com`。

    b. 按一下頁面底部的 [新增] 來單一登入應用程式。

    > [!NOTE] 
    > 請注意，您必須使用實際的「登入 URL」來更新此值。 若要取得此值，請連絡 Slack 支援小組。

6. 於 [在 Slack 設定單一登入] 頁面上，按 [下載憑證]，然後將檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

7.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Slack 公司網站。

8.  瀏覽至 [Microsoft Azure AD]，然後移至 [小組設定]。

    ![在應用程式端設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

9.  在 [小組設定] 區段中，按一下 [驗證] 索引標籤，然後按一下 [變更設定]。

    ![在應用程式端設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

10. 在 [SAML 驗證設定]  對話方塊上，執行下列步驟：

    ![在應用程式端設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  在 [SAML 2.0 端點 (HTTP)] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [SAML SSO URL] 值。

    b.  在 [識別提供者簽發者] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [簽發者 URL] 值。

    c.  在記事本中開啟您下載的憑證檔，將其內容複製到剪貼簿上，然後貼到 [公開憑證] 文字方塊中。

    d.  取消選取 [允許使用者變更其電子郵件地址] 。

    e.  選取 [允許使用者選擇自己的使用者名稱] 。

    f.  針對 [必須使用您的小組的驗證]，選取 [此為選擇性]。

    g.  按一下 [儲存組態] 。

11. 在傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
    
    ![Azure AD 單一登入][10]

12. 在 [單一登入確認] 頁面上，按一下 [完成]。  
    
    ![Azure AD 單一登入][11]



### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_09.png)

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_05.png)

    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c. 按 [下一步] 。

6.  在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_06.png)

    a. 在 [名字] 文字方塊中，輸入 **Britta**。  

    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。

    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d. 在 [角色] 清單中選取 [使用者]。

    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_07.png)

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_08.png)

    a. 記下 [新密碼] 的值。

    b. 按一下頁面底部的 [新增] 。   



### <a name="creating-a-slack-test-user"></a>建立 Slack 測試使用者

本節目標是在 Slack 中建立名為 Britta Simon 的使用者。 Slack 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 嘗試存取 Slack 時，如果使用者還不存在，就會建立新使用者。

> [!NOTE] 
> 如果您需要手動建立使用者，就需要連絡 Slack 支援小組。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是要授與 Britta Simon 對 Slack 的存取權，讓她能夠使用 Azure 單一登入。
    
![指派使用者][200]

**若要將 Britta Simon 指派到 Slack，請執行以下步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
    
    ![指派使用者][201]

2. 在應用程式清單中，選取 [Slack]。
    
    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. 在頂端的功能表中，按一下 [使用者] 。
    
    ![指派使用者][203]

4. 在 [使用者] 清單中，選取 [Britta Simon] 。

5. 在底部的工具列中，按一下 [指派] 。
    
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。
 
當您在存取面板中按一下 Slack 圖格時，應該會自動登入您的 Slack 應用程式。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-slack-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-slack-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-slack-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-slack-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-slack-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


