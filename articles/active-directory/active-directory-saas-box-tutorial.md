<properties 
    pageTitle="教學課程：Azure Active Directory 與 Box 整合 | Microsoft Azure" 
    description="了解如何使用 Box 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/13/2016" 
    ms.author="jeedes" />




#教學課程：Azure Active Directory 與 Box 整合


  
本教學課程的目的是要示範 Azure 與 Box 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Box 中的測試租用戶
  
完成本教學課程之後，您指派給 Box 的 Azure AD 使用者就能夠單一登入您 Box 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Box 的應用程式整合
2.  設定單一登入
3.  設定使用者和群組佈建
4.  指派使用者

![案例](./media/active-directory-saas-box-tutorial/IC769537.png "案例")



##啟用 Box 的應用程式整合
  
本節的目的是概述如何啟用 Box 的應用程式整合。

###若要啟用 Box 的應用程式整合，請執行下列步驟：

1.  在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![應用程式](./media/active-directory-saas-box-tutorial/IC700994.png "應用程式")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-box-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從資源庫加入應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-box-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Box**。

    ![應用程式庫](./media/active-directory-saas-box-tutorial/IC701023.png "應用程式庫")

7.  在結果窗格中，選取 [Box]，然後按一下 [完成] 以加入應用程式。

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Box 中進行驗證。在此程序中，您必須將中繼資料上傳至 Box.com。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure 傳統入口網站的 [Box] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769538.png "設定單一登入")

2.  在 [要如何讓使用者登入 Box] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769539.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Box 租用戶 URL] 文字方塊中，輸入您的 Box 租用戶 URL (例如︰https://<mydomainname>.box.com)，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-box-tutorial/IC669826.png "設定應用程式 URL")

4.  在 [設定在 Box 單一登入] 頁面上，按一下 [下載中繼資料] 來下載您的中繼資料，然後將資料檔儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC669824.png "設定單一登入")

5.  將該中繼資料檔案轉寄給 Box 支援小組。支援小組需要為您設定單一登入。

6.  選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769540.png "設定單一登入")
##設定使用者佈建
  
本節的目的是要說明如何對 Box 啟用 Active Directory 使用者帳戶的佈建。

###若要設定單一登入，請執行下列步驟：

1. 在 Azure 傳統入口網站中的 [Box] 應用程式整合頁面上，按一下 [設定使用者佈建] 以開啟 [設定使用者佈建] 對話方塊。

	![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769541.png "啟用自動使用者佈建")

2. 在 [**啟用使用者佈建到 Box**] 對話方塊頁面上，按一下 [**啟用使用者佈建**]。

	![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769544.png "啟用自動使用者佈建")

3. 在 [登入以授與 Box 存取權] 頁面上，提供必要的認證，然後按一下 [授權]。

	![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769546.png "啟用自動使用者佈建")


4. 按一下 [授與 Box 存取權]，以授權進行此作業並返回 Azure 傳統入口網站。

	![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769549.png "啟用自動使用者佈建")


5. 在 [佈建選項] 頁面上，[要佈建的物件類型] 核取方塊可讓您選取是否在使用者物件之外，也在 Box 佈建群組物件。如需詳細資訊，請參閱以下＜指派使用者和群組＞一節。


6. 若要完成設定，請按一下 [完成] 按鈕。

	![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769551.png "啟用自動使用者佈建")



##指派測試使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Box，請執行下列步驟：

1. 在 Azure 傳統入口網站中建立測試帳戶。

2. 在 [Box] 應用程式整合頁面上，按一下 [指派使用者]。

	![指派使用者](./media/active-directory-saas-box-tutorial/IC769552.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

	![是](./media/active-directory-saas-box-tutorial/IC767830.png "是")
  
請等候 10 分鐘並確認帳戶已同步至 Box。

在第一個驗證步驟中，您可以在 Azure 傳統入口網站的 Box 應用程式整合頁面上，按一下 D 的儀表板來檢查佈建狀態。

![儀表板](./media/active-directory-saas-box-tutorial/IC769553.png "儀表板")

成功完成的使用者佈建週期會以相關狀態表示：

![整合狀態](./media/active-directory-saas-box-tutorial/IC769555.png "整合狀態")


在 Box 租用戶中，已同步處理的使用者會列在 [管理主控台] 的 [管理使用者] 之下。

![整合狀態](./media/active-directory-saas-box-tutorial/IC769556.png "整合狀態")


##指派使用者和群組

Azure 傳統入口網站的 [Box] > [使用者和群組] 索引標籤可讓您指定應該授與哪些使用者和群組 Box 的存取權。指派使用者或群組會導致下列事項發生︰

* Azure AD 允許指派的使用者 (直接指派或群組成員資格) 驗證 Box。如果使用者未經指派，則 Azure AD 不會允許他們登入 Box，並會在 Azure AD 登入頁面傳回錯誤。

* Box 的應用程式圖格會加入使用者的[應用程式啟動程式](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)中。

* 如已啟用自動佈建，則指派的使用者及/或群組就會加入佈建佇列進行自動佈建。

    * 如果只設定要佈建使用者物件，則所有直接指派的使用者都會放在佈建佇列中，而任何指派群組成員的所有使用者也會放在佈建佇列中。
    
    * 如果設定要佈建的是群組物件，則所有指派的群組物件以及這些群組成員的所有使用者都會佈建到 Box。群組和使用者成員資格都會保留寫入 Box。
    
您可以使用 [屬性] > [單一登入] 索引標籤來設定，在 SAML 驗證期間，要向 Box 呈現哪些使用者屬性 (或宣告)；使用 [屬性] > [佈建] 索引標籤來設定，在佈建作業期間，使用者及群組屬性如何從 Azure AD 流向 Box。如需詳細資訊，請參閱底下的資源。


## 其他資源

* [自訂 SAML 權杖發出的宣告](active-directory-saml-claims-customization.md)
* [佈建︰自訂屬性對應](active-directory-saas-customizing-attribute-mappings.md)
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0720_2016-->