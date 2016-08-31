<properties
	pageTitle="教學課程：Azure Active Directory 與 Asana 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 Asana 之間的單一登入。"
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jeedes"/>


# 教學課程：Azure Active Directory 與 Asana 整合

在本教學課程中，您會了解如何整合 Asana 與 Azure Active Directory (Azure AD)。

Asana 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Asana 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Asana (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件

若要設定與 Asana 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 **Asana** 單一登入功能的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## 案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Asana
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 Asana
若要設定 Asana 與 Azure AD 整合，您需要從資源庫將 Asana 加入受管理的 SaaS 應用程式清單中。

**若要從資源庫加入 Asana，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。

	![Active Directory][1]

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

	![應用程式][2]

4. 按一下頁面底部的 [新增]。

	![應用程式][3]

5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫加入應用程式]。

	![應用程式][4]

6. 在搜尋方塊中，輸入 **Asana**。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)

7. 在結果窗格中，選取 [Asana]，然後按一下 [完成] 加入應用程式。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

##  設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Asana 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Asana 與 Azure AD 中互相對應的使用者。換句話說，必須建立 Azure AD 使用者和 Asana 中相關使用者之間的連結關聯性。建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值，指派為 Asana 中 [使用者名稱] 的值。

若要設定及測試與 Asana 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 Asana 測試使用者](#creating-an-Asana-test-user)** - 讓 Britta Simon 在 Asana 中有一個對應身分連結到她的 Azure AD 代表身分。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入

本節的目標是在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 Asana 應用程式中設定單一登入。

**若要使用 Asana 設定 Azure AD 單一登入，請執行下列步驟：**

1. 按一下頂端功能表中的 [快速啟動]。

	![設定單一登入][6]
2. 在傳統入口網站的 [Asana] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

	![設定單一登入][7]

3. 在 [要如何讓使用者登入 Asana] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
 	
	![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)

4. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟：

	![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)


    a.在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://app.asana.com`

	c.按 [下一步]。

5. 於 [在 Asana 設定單一登入] 頁面上，按 [下載憑證]，然後將檔案儲存在您的電腦中。此外，複製 SAML SSO URL 的值。
	
	![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)

8. 以滑鼠右鍵按一下憑證，然後使用記事本或您慣用的文字編輯器開啟憑證檔案。複製開始和結束憑證標題之間的內容。這是將在 Asana 中用來設定 SSO 的 X.509 憑證。

6. 在不同的瀏覽器視窗中，登入您的 Asana 應用程式。若要在 Asana 中設定 SSO，請按一下螢幕右上角的工作區名稱來存取工作區設定。然後，按一下 [<您的工作區名稱> 設定]。

	![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

7. 在 [組織設定] 視窗中，按一下 [管理]。然後按一下 [成員必須透過 SAML 登入] 以啟用 SSO 組態。執行下列步驟：

	![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)

	a.在 [登入頁面 URL] 文字方塊中，貼上取自 Azure AD 的 SAML 登入 URL。

	b.在 [X.509 憑證] 文字方塊中，貼上從 Azure AD 複製而來的 X.509 憑證。

9. 按一下 [儲存]。如需進一步協助，請移至[用於設定 SSO 的 Asana 指南](https://asana.com/guide/help/premium/authentication#gl-saml)。

7. 在 Azure AD 中，移至 [設定在 Asana 單一登入] 頁面，選取單一登入設定確認，然後按 [下一步]。
	
	![Azure AD 單一登入][10]

8. 在 [單一登入確認] 頁面上，按一下 [完成]。
  	
	![Azure AD 單一登入][11]


### 建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
	
	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png)

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表中的 [使用者]。
	
	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png)

4. 若要開啟 [加入使用者] 對話方塊，請按一下底部工具列中的 [加入使用者]。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png)

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟：
 
	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png)

    a.針對 [使用者類型]，選取 [您組織中的新使用者]。

    b.在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c.按 [下一步]。

6.  在 [使用者設定檔]對話方塊頁面上，執行下列步驟：

	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png)

    a.在 [名字] 文字方塊中，輸入 **Britta**。

    b.在 [姓氏] 文字方塊中，輸入 **Simon**。

    c.在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d.在 [角色] 清單中選取 [使用者]。

    e.按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png)

8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：

	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png)

    a.記下 [新密碼] 的值。

    b.按一下 [完成]。



### 建立 Asana 測試使用者

在本節中，您要在 Asana 中建立名為 Britta Simon 的使用者。

1. 在 [Asana] 移至左面板上的 [小組] 區段。按一下加號按鈕。

	![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png)

2. 在文字方塊中輸入電子郵件 britta.simon@contoso.com，然後選取 [邀請]。
3. 按一下 [傳送邀請]。新的使用者會在她的電子郵件帳戶收到一封電子郵件。她將需要建立並驗證帳戶。


### 指派 Azure AD 測試使用者

在本節中，您會把 Asana 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200]

**若要將 Britta Simon 指派到 Asana，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

	![指派使用者][201]

2. 在應用程式清單中，選取 [Asana]。

	![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png)

1. 在頂端的功能表中，按一下 [使用者]。

	![指派使用者][203]

1. 在 [所有使用者] 清單中，選取 [Britta Simon]。

2. 在底部的工具列中，按一下 [指派]。

	![指派使用者][205]


### 測試單一登入

本節的目標是要測試您的 Azure AD 單一登入。

移至 Asana 登入頁面。在 [電子郵件地址] 文字方塊中插入電子郵件地址 britta.simon@contoso.com。讓 [密碼] 文字方塊保持空白，然後按一下 [登入]。系統會將您重新導向至 Azure AD 登入頁面。完成您的 Azure AD 認證。現在，您已在 Asana 上登入。

## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->