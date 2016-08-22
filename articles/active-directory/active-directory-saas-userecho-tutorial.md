<properties
	pageTitle="教學課程：Azure Active Directory 與 UserEcho 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 UserEcho 之間的單一登入。"
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="jeedes"/>


# 教學課程：Azure Active Directory 與 UserEcho 整合

本教學課程旨在說明如何將 UserEcho 與 Azure Active Directory (Azure AD) 整合。將 UserEcho 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 UserEcho 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 UserEcho (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件 

若要設定 Azure AD 與 UserEcho 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 UserEcho 單一登入功能的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

 
## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 UserEcho
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 UserEcho
若要設定將 UserEcho 整合到 Azure AD 中，您需要從資源庫將 UserEcho 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 UserEcho，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。

	![Active Directory][1]

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

	![應用程式][2]

4. 按一下頁面底部的 [新增]。

	![應用程式][3]

5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫加入應用程式]。

	![應用程式][4]

6. 在搜尋方塊中，輸入 **UserEcho**。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)

7. 在結果窗格中，選取 [UserEcho]，然後按一下 [完成] 以新增應用程式。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)

##  設定並測試 Azure AD 單一登入
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 UserEcho 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 UserEcho 與 Azure AD 中互相對應的使用者。換句話說，必須在 Azure AD 使用者與 UserEcho 中的相關使用者之間建立連結關聯性。建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 UserEcho 中 **Username** 的值。
 
若要設定及測試與 UserEcho 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 UserEcho 測試使用者](#creating-a-userecho-test-user)** - 在 UserEcho 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入

本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 UserEcho 應用程式中設定單一登入。




**若要設定與 UserEcho 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [UserEcho] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

	![設定單一登入][6]

2. 在 [要如何讓使用者登入 UserEcho] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png)

3. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟：

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png)

    a.在 [登入 URL] 文字方塊中，輸入使用者用來登入 UserEcho 應用程式的 URL (例如：*https://fabrikam.UserEcho.com/*)。

    b.按 [下一步]。
 
 
4. 在 [設定在 UserEcho 單一登入] 頁面上，執行下列步驟：

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png)

    a.按一下 [下載憑證]，然後將檔案儲存在您的電腦上。

    b.按 [下一步]。


1. 在另一個瀏覽器視窗中，以系統管理員身分登入您的 UserEcho 公司網站。

1. 在頂端工具列中，按一下您的使用者名稱以展開功能表，然後按一下 [設定]。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

1. 按一下 [整合]。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png)


1. 按一下 [網站]，然後按一下 [單一登入 (SAML2)]。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png)


1. 在 [單一登入 (SAML)] 頁面上，執行下列步驟：

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)

    a.在 [已啟用 SAML] 選取 [是]。

    b.在 Azure 傳統入口網站的 [設定在 UserEcho 單一登入] 對話方塊頁面上，複製 [單一登入服務 URL] 值，然後將它貼至 [SAML SSO URL] 文字方塊中。

    c.在 Azure 傳統入口網站的 [設定在 UserEcho 單一登入] 對話頁面上，複製 [遠端登出 URL] 值，然後將它貼至 [遠端登出 URL] 文字方塊中。

    d.在記事本中開啟下載的憑證，複製其內容，然後貼到 [X.509 憑證] 文字方塊中。

    e.按一下 [儲存]。


6. 在 Azure 傳統入口網站中，選取單一登入設定確認，然後按 [下一步]。

	![Azure AD 單一登入][10]

7. 在 [單一登入確認] 頁面上，按一下 [完成]。

	![Azure AD 單一登入][11]




### 建立 Azure AD 測試使用者
本節的目標是要在 Azure 傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png)

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表中的 [使用者]。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png)
 
4. 若要開啟 [加入使用者] 對話方塊，請按一下底部工具列中的 [加入使用者]。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png)

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟：

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png)

    a.針對 [使用者類型]，選取 [您組織中的新使用者]。

    b.在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c.按 [下一步]。

6.  在 [使用者設定檔] 對話方塊頁面上，執行下列步驟：

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png)
 
    a.在 [名字] 文字方塊中，輸入 **Britta**。

    b.在 [姓氏] 文字方塊中，輸入 **Simon**。

    c.在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d.在 [角色] 清單中，選取 [使用者]。按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png)
 
8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：

	![建立 Azure AD 測試使用者](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png)
  
    a.記下 [新密碼] 的值。

    b.按一下 [完成]。

  
 
### 建立 UserEcho 測試使用者

本節的目標是要在 UserEcho 中建立一個名為 Britta Simon 的使用者。

**若要在 UserEcho 中建立名為 Britta Simon 的使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 UserEcho 公司網站。

1. 在頂端工具列中，按一下您的使用者名稱以展開功能表，然後按一下 [設定]。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

1. 按一下 [使用者] 以展開 [使用者] 區段。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

1. 按一下 [使用者]。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

1. 按一下 [邀請新使用者]。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)


1. 在 [邀請新使用者] 對話方塊中，執行下列步驟：

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a.在 [名稱] 文字方塊中，輸入 **Britta Simon**。

    b.在 [電子郵件] 文字方塊中，輸入 Britta 在 Azure 傳統入口網站中的電子郵件地址。

    c.按一下 [邀請]。

會傳送邀請給 Britta，邀請可讓她開始使用 UserEcho。



### 指派 Azure AD 測試使用者

本節的目標是要授權 Britta Simon 存取 UserEcho，讓她能夠使用 Azure 單一登入。

![指派使用者][200]

**若要將 Britta Simon 指派給 UserEcho，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

	![指派使用者][201]

2. 在應用程式清單中，選取 [UserEcho]。

	![設定單一登入](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png)

1. 在頂端的功能表中，按一下 [使用者]。

	![指派使用者][203]

1. 在 [使用者] 清單中，選取 [Britta Simon]。

2. 在底部的工具列中，按一下 [指派]。

	![指派使用者][205]



### 測試單一登入

本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。當您在存取面板中按一下 [UserEcho] 磚時，應該會自動登入您的 UserEcho 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->