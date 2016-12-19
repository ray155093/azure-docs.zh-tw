---
title: "教學課程：Azure Active Directory 與 Marketo 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Marketo 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2d31f2643ac39fb6227b57d2b064058af1dbe20d


---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>教學課程：Azure Active Directory 與 Marketo 整合
在本教學課程中，您會了解如何整合 Marketo 與 Azure Active Directory (Azure AD)。

Marketo 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Marketo 的人員
* 您可以讓使用者利用自己的 Azure AD 帳戶，來自動登入 Marketo (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Marketo 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 Marketo 單一登入的訂用帳戶

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

1. 從資源庫新增 Marketo
2. 設定並測試 Azure AD 單一登入

## <a name="adding-marketo-from-the-gallery"></a>從資源庫新增 Marketo
若要設定 Marketo 與 Azure AD 整合，您需要從資源庫將 Marketo 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫加入 Marketo，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Marketo**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)
7. 在結果窗格中，選取 [Marketo]，然後按一下 [完成] 加入應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Marketo 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Marketo 與 Azure AD 中互相對應的使用者。 換句話說，必須要建立某位 Azure AD 使用者與 Marketo 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 Marketo 中 **Username** 的值。

若要設定及測試與 Marketo 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Marketo 測試使用者](#creating-a-predictix-price-reporting-test-user)** - 使 Marketo 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，然後在您的 Marketo 應用程式中設定單一登入。

**若要使用 Marketo 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在傳統入口網站的 [Marketo] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [您希望使用者如何登入 Marketo] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png) 
   
    a. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://saml.marketo.com/sp`
   
    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://login.marketo.com/saml/assertion/\<munchkinid\>`
   
    c. 按 [下一步] 
4. 在 [設定在 Marketo 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
   
    b. 按 [下一步] 。
5. 若要取得您的應用程式的 Munchkin 識別碼，請使用系統管理員認證登入 Marketo，執行以下動作︰
   
    a. 使用系統管理員認證登入Marketo 應用程式。
   
    b. 按一下頂端導覽窗格中的 [管理] 按鈕。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 瀏覽至 [整合] 功能表，然後按一下 [Munchkin] 連結
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. 複製螢幕上顯示的 Munchkin 識別碼，完成 Azure AD 設定精靈中的回覆 URL。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)
6. 若要設定應用程式中的 SSO，請遵循下列步驟︰
   
   a. 使用系統管理員認證登入Marketo 應用程式。
   
   b. 按一下頂端導覽窗格中的 [管理] 按鈕。
   
   ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
   c. 瀏覽至 [整合] 功能表，然後按一下 [單一登入] 連結。
   
   ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
   d. 若要啟用 SAML 設定，按一下 [編輯] 按鈕。
   
   ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
   e. **啟用**單一登入設定
   
   f. 輸入簽發者識別碼 (從 Azure AD 設定精靈中複製)。
   
   g. 在 [實體識別碼] 文字方塊中，輸入 URL **http://saml.marketo.com/sp**
   
   h. [使用者識別碼位置] 選取 [名稱識別碼元素]
   
   ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
   > [!NOTE]
   > 如果您的使用者識別碼不是 UPN 值，則至 [屬性] 索引標籤中變更其值。
   > 
   > 
   
   i. 上傳您從 Azure AD 設定精靈下載的憑證。 儲存設定。
   
   j. 編輯 [重新導向頁面] 設定。
   
   k. 從 Azure AD 設定精靈的 [登入 URL] 文字方塊中複製登入 URL。
   
   l. 從 Azure AD 設定精靈的 [登出 URL] 文字方塊中複製登出 URL。
   
   m. 在 [錯誤 URL] 中，複製您的 Marketo 執行個體 URL，並按一下 [儲存] 按鈕以儲存設定。
   
   ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)
7. 若要啟用使用者的 SSO，完成下列動作：
   
    a. 使用系統管理員認證登入Marketo 應用程式。
   
    b.這是另一個 C# 主控台應用程式。 按一下頂端導覽窗格中的 [管理]  按鈕。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 瀏覽至 [安全性] 功能表，然後按一下 [登入設定]。 
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. 勾選 [需要 SSO] 選項，並 [儲存] 設定。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)
8. 在傳統入口網站中，選取單一登入組態確認，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
9. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟： ![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔] 對話方塊頁面上，執行下列步驟：![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png) 
   
   a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
   b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-an-marketo-test-user"></a>建立 Marketo 測試使用者
在本節中，您要在 Marketo 中建立名為 Britta Simon 的使用者。 請遵循下列步驟在 Marketo 平台中建立使用者。

1. 使用系統管理員認證登入Marketo 應用程式。
2. 按一下頂端導覽窗格中的 [管理]  按鈕。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
3. 瀏覽至 [安全性] 功能表，然後按一下 [使用者與角色]。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  
4. 按一下 [使用者] 索引標籤上的 [邀請新使用者]  連結。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 
5. 在 [邀請新使用者] 精靈中填寫下列資訊。
   
    a. 在 [電子郵件] 文字方塊中輸入使用者的電子郵件地址。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. 在 [名字] 文字方塊中輸入名字。
   
    c. 在 [姓氏] 文字方塊中輸入姓氏。
   
    d. 按一下 [下一步]。
6. 在 [權限]  索引標籤中選取使用者的 [角色]，然後按一下 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. 按一下 [傳送] 按鈕傳送使用者邀請。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)
8. 使用者會收到電子郵件通知，他/她必須按一下連結，並變更密碼以啟動帳戶。 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會將 Marketo 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Marketo，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Marketo] 。
   
    ![設定單一登入](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Marketo] 圖格時，應該會自動登入您的 Marketo 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


