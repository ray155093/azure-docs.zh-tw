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
ms.sourcegitcommit: 9c027decf5d982519bc9f41aa9588fa431ef3975
ms.openlocfilehash: 17ad8938cbee539e74f87210077a12c72e777edc
ms.lasthandoff: 02/24/2017


---
# <a name="tutorial-azure-ad-integration-with-slack"></a>教學課程：Azure AD 與 Slack 整合

在本教學課程中，您會了解如何整合 Slack 與 Azure Active Directory (Azure AD)。

在整合 Slack 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Slack。
* 讓使用者使用其 Azure AD 帳戶的單一登入 (SSO) 自動登入 Slack。
* 在 Azure 入口網站中集中管理您的帳戶。

若要了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 Slack 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 Slack SSO 的訂用帳戶

> [!NOTE]
> 我們不建議使用生產環境測試本教學課程中的步驟。

若要測試本教學課程中的步驟，請遵循下列建議：

* 只在必要時才使用生產環境。
* 如果您沒有 Azure AD 試用環境，請取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD SSO。 要遵循的案例包含兩個主要建置組塊︰

* 從資源庫新增 Slack
* 設定並測試 Azure AD SSO

## <a name="add-slack-from-the-gallery"></a>從資源庫新增 Slack
若要設定 Slack 與 Azure AD 的整合，請執行下列步驟，從資源庫將 Slack 新增到受管理的 SaaS 應用程式清單：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![[Azure Active Directory] 按鈕][1]

3. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![[企業應用程式] 刀鋒視窗上的 [所有應用程式] 按鈕][2]

4. 在 [所有應用程式] 對話方塊的頂端，按一下 [新增]。

    ![[所有應用程式] 對話方塊中的 [新增] 按鈕][3]

5. 在 [搜尋方塊] 中，輸入 **Slack**。

    ![[新增應用程式] 搜尋方塊](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. 在結果窗格中，選取 [Slack]，然後按一下 [新增] 按鈕以新增應用程式。

    ![在結果窗格中選取 [Slack]](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO
在本節中，您會以測試使用者 "Britta Simon" 使用 Slack 設定及測試 Azure AD SSO。

若要讓 SSO 運作，Azure AD 必須在 Azure AD 使用者與其在 Slack 中互相對應的使用者之間建立連結關聯性。 建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 Slack 中 **Username** 的值。

若要設定及測試與 Slack 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. [設定 Azure AD SSO](#configuring-azure-ad-single-sign-on)，讓使用者能夠使用這項功能。
2. [建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)，以使用者 Britta Simon 測試 Azure AD SSO。
3. [建立 Slack 測試使用者](#creating-a-slack-test-user)，讓 Azure AD 使用者 Britta Simon 獲得可供連結的 Slack 對應身分。
4. [指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)，讓使用者 Britta Simon 能夠使用 Azure AD SSO。
5. [測試 SSO](#testing-single-sign-on)，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您會執行下列步驟，在 Azure 入口網站中啟用 Azure AD SSO，並在 Slack 應用程式中設定 SSO：

1. 在 Azure 入口網站的 [Slack] 應用程式整合頁面上，按一下 [單一登入]。

    ![Slack 應用程式整合頁面][4]

2. 在 [單一登入] 對話方塊的 [模式] 清單中，選取 [SAML 登入] 來啟用 SSO。

    ![[單一登入] 對話方塊](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. 在 [Slack 網域與 URL] 下，執行下列步驟：

    ![[單一登入] 對話方塊](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a. 在 [登入 URL] 方塊中，輸入使用命名慣例「https://<company name>.slack.com」的 URL。

    b. 在 [識別碼] 方塊中，輸入 **https://slack.com**。

    > [!NOTE]
    > 上述值並非真正的值。 在此，我們建議您使用唯一的 URL 和識別碼值。 稍候，您會使用實際的 URL 和識別碼更新這兩個值。 若要取得這兩個值，請連絡 [Slack 支援小組](https://slack.com/help/contact)。

4. Slack 應用程式需要以特定格式顯示的安全性聲明標記語言 (SAML) 判斷提示。 請在 Slack 應用程式整合頁面的 [使用者屬性] 區段中設定宣告並管理屬性值，如下列螢幕擷取畫面所示︰

    ![在 [使用者屬性] 區段中設定宣告](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. 在 [單一登入] 對話方塊的 [使用者屬性] 區段中，選取 [user.mail] 做為 [使用者識別碼]。 針對下表中的每個資料列，執行下列步驟：

    | 屬性名稱 | 屬性值 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. 按一下 [新增屬性] 按鈕。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    b. 在 [新增屬性] 對話方塊的 [名稱] 方塊中，輸入上表中 [屬性名稱] 欄的名字。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)

    c. 在 [值] 方塊中，輸入上表中 [屬性值] 欄的第一個值。

    d. 按一下 [確定] 。

    e. 針對隨後的三個表格列，重複步驟 "a" 到 "d"。

6. 在 [SAML 簽署憑證] 之下，按一下 [建立新憑證]。

    ![建立憑證](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. 在 [建立新憑證] 對話方塊中，按一下 [行事曆] 按鈕，選取到期日，然後按一下 [儲存]。

    ![選取憑證到期日](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. 在 [SAML 簽署憑證] 下，選取 [啟用新憑證] 核取方塊，然後按一下 [儲存]。

    ![啟用 SAML 簽署憑證](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. 在 [變換憑證] 快顯視窗中，按一下 [確定]。

    ![[變換憑證] 快顯視窗](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. 在 [SAML 簽署憑證] 下，按一下 [憑證 (Base64)]，然後將憑證檔案儲存到本機磁碟機。

    ![將憑證儲存到本機磁碟機](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. 在 [Slack 組態] 下，按一下 [設定 Slack] 以開啟 [設定登入] 視窗。

    ![按一下 [設定 Slack] 以開啟 [設定登入] 視窗](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    ![[登入設定] 視窗](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. 開啟新的瀏覽器視窗，然後以系統管理員身分登入您的 Slack 公司網站。

13. 移至 [Microsoft Azure AD]，然後移至 [小組設定]。

    ![Slack 公司網站上的 Microsoft Azure AD [小組設定] 按鈕](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. 在 [小組設定] 下，按一下 [驗證] 索引標籤，然後按一下 [變更設定]。

    ![[小組設定] 頁面上的 [變更設定] 按鈕](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. 在 [SAML 驗證設定] 對話方塊中，執行下列步驟：

    ![[SAML 驗證設定] 對話方塊](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a. 在 [SAML 2.0 端點 (HTTP)] 方塊中，輸入來自 Azure AD 應用程式組態視窗的 [SAML 單一登入服務 URL] 值。

    b. 在 [識別提供者簽發者] 方塊中，輸入得自 Azure AD 應用程式組態視窗的 [SAML 實體識別碼] 值。

    c. 在記事本中開啟下載的憑證，複製其內容，然後貼到 [公開憑證] 方塊中。

    d. 針對您的 Slack 小組適當地設定上述三個設定。 如需設定的詳細資訊，請參閱 [Slack 單一登入指南](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack)。

    e. 按一下 [儲存組態] 。

    <!-- 取消選取 [允許使用者變更其電子郵件地址] 。

    e. 選取 [允許使用者選擇自己的使用者名稱] 。

    f. 針對 [必須使用您的小組的驗證]，選取 [此為選擇性]。 -->


### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會執行下列步驟，在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者：

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![[Azure Active Directory] 按鈕](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。

    ![Azure AD [所有使用者] 按鈕](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. 在 [所有使用者] 對話方塊的頂端，按一下 [新增]。

    ![[新增使用者] 對話方塊中的 [新增] 按鈕](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，輸入下列資訊：

    ![[使用者] 對話方塊](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下 [建立] 。

### <a name="create-a-slack-test-user"></a>建立 Slack 測試使用者

本節會在 Slack 中建立名為 Britta Simon 的使用者。 Slack 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 如果 Slack 中還沒有該使用者，當您嘗試存取 Slack 時，就會建立新的使用者。

> [!NOTE]
> 如果您需要手動建立使用者，請連絡 [Slack 支援小組](https://slack.com/help/contact)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Slack 的存取權授與使用者 Britta Simon，讓她能夠使用 Azure SSO。

![指派 Azure SSO 的使用者][200]

若要將使用者 Britta Simon 指派給 Slack，請執行以下步驟：

1. 在 Azure 入口網站中，開啟應用程式檢視，移至目錄檢視，移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201]

2. 在 [應用程式] 清單中，選取 [Slack]。

    ![Azure 入口網站應用程式清單](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. 在左窗格中，按一下 [使用者和群組]。

    ![左窗格的 [使用者和群組] 按鈕][202]

4. 按一下 [新增] 按鈕，然後在 [新增指派] 刀鋒視窗上，選取 [使用者和群組]。

    ![[新增] 按鈕和 [新增指派] 刀鋒視窗][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]。

6. 按一下 [選取] 按鈕。

7. 在 [新增指派] 刀鋒視窗上，按一下 [指派] 按鈕。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD SSO 組態。

若要測試組態，請移至存取面板，然後按一下 [Slack] 圖格。 使用者應該會自動登入 Slack 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
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

