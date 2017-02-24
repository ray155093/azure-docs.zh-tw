---
title: "教學課程：Azure Active Directory 與 Slack 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Slack 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 14972d3e1212fb0cf6653bd4a693470425294d2c
ms.openlocfilehash: c975231ea18c7c1853d9b20fc49542c10ef9abcc


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>教學課程：Azure Active Directory 與 Slack 整合

在本教學課程中，您會了解如何整合 Slack 與 Azure Active Directory (Azure AD)。

Slack 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Slack
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Slack (單一登入)
- 您可以在 Azure 管理入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 Slack 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Slack 單一登入功能的訂用帳戶


> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Slack
2. 設定並測試 Azure AD 單一登入


## <a name="adding-slack-from-the-gallery"></a>從資源庫新增 Slack
若要設定將 Slack 整合到 Azure AD 中，您需要從資源庫將 Slack 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Slack，請執行下列步驟：**

1. 在 **[Azure 管理入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增] 按鈕。

    ![應用程式][3]

4. 在 [搜尋方塊] 中，輸入 **Slack**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

5. 在結果窗格中，選取 [Slack]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Slack 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Slack 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Slack 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 Slack 中 **Username** 的值。

若要設定及測試與 Slack 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Slack 測試使用者](#creating-a-slack-test-user)** - 在 Slack 中建立一個與 Azure AD 中代表 Britta Simon 的項目連結的 Britta Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 管理入口網站中啟用 Azure AD 單一登入，並在您的 Slack 應用程式中設定單一登入。

**若要使用 Slack 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 管理入口網站的 [Slack] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，選取 [SAML 型登入] 做為 [模式]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. 在 [Slack 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<company name>.slack.com`

    b. 在 [識別碼] 文字方塊中，輸入：`https://slack.com`。

    > [!NOTE] 
    > 請注意這些不是真正的值。 您必須使用實際的「登入 URL」及「識別碼」來更新這些值。 在此建議您在 [識別碼] 中使用唯一的 URL 值。 請連絡 [Slack 支援小組](https://slack.com/help/contact)以取得這些值。 

4. Slack 應用程式需要特定格式的 SAML 判斷提示。 請設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。
    
    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. 在 [單一登入] 對話方塊內的 [使用者屬性] 區段中，選取 [user.mail] 當作 [使用者識別碼]，並在下表顯示的每個列上執行下列步驟：
    
    | 屬性名稱 | 屬性值 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    
    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。
    
    c. 在 [值] 清單中，選取該列所顯示的值。
    
    d. 按一下 [確定] 

6. 在 [SAML 簽署憑證] 區段中，按一下 [建立新憑證]。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. 在 [建立新憑證] 對話方塊中，按一下行事曆圖示並選取 [到期日]。 然後按一下 [儲存] 按鈕。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. 在 [SAML 簽署憑證] 區段中，選取 [啟用新憑證] 並按一下 [儲存] 按鈕。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. 在 [變換憑證] 快顯視窗上，按一下 [確定]。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png) 

11. 在 [Slack 組態] 區段上，按一下 [設定 Slack] 以開啟 [設定登入] 視窗。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png) 

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Slack 公司網站。

13.  瀏覽至 [Microsoft Azure AD]，然後移至 [小組設定]。

    ![在應用程式端設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14.  在 [小組設定] 區段中，按一下 [驗證] 索引標籤，然後按一下 [變更設定]。

    ![在應用程式端設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. 在 [SAML 驗證設定]  對話方塊上，執行下列步驟：

    ![在應用程式端設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  在 [SAML 2.0 端點 (HTTP)] 文字方塊中，放入來自 Azure AD 應用程式組態視窗的 [SAML 單一登入服務 URL] 值。

    b.  在 [識別提供者簽發者] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [SAML 實體識別碼] 值。

    c.  在記事本中開啟您下載的憑證檔，將其內容複製到剪貼簿上，然後貼到 [公開憑證] 文字方塊中。

    d. 針對您的 Slack 小組適當地設定上述三個設定。 如需這些這設定的詳細資訊，請參閱這裡的 **Slack 的 SSO 設定指南**。 `https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack`

    e.  按一下 [儲存組態] 。
     
    <!-- 取消選取 [允許使用者變更其電子郵件地址] 。

    e.  選取 [允許使用者選擇自己的使用者名稱] 。

    f.  針對 [必須使用您的小組的驗證]，選取 [此為選擇性]。 -->
  

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 管理入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。 



### <a name="creating-a-slack-test-user"></a>建立 Slack 測試使用者

本節目標是在 Slack 中建立名為 Britta Simon 的使用者。 Slack 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 嘗試存取 Slack 時，如果使用者還不存在，就會建立新使用者。

> [!NOTE]
> 如果您需要手動建立使用者，就需要連絡 [Slack 支援小組](https://slack.com/help/contact)。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Slack 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Slack，請執行以下步驟：**

1. 在 Azure 管理入口網站中，開啟應用程式檢視，然後瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Slack]。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    


### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Slack 圖格時，應該會自動登入您的 Slack 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


