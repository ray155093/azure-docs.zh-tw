<properties 
    pageTitle="教學課程：Azure Active Directory 與 Zendesk 整合 | Microsoft Azure" 
    description="了解如何使用 Zendesk 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-active-directory-integration-with-zendesk"></a>教學課程：Azure Active Directory 與 Zendesk 整合
  
本教學課程的目的是要示範 Azure 與 Zendesk 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂用帳戶
-   Zendesk 租用戶
  
完成本教學課程之後，您指派給 Zendesk 的 Azure AD 使用者就能夠從您的 Zendesk 公司網站 (服務提供者起始登入)，或使用 [存取面板](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Zendesk 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario")

##<a name="enabling-the-application-integration-for-zendesk"></a>啟用 Zendesk 的應用程式整合
  
本節的目的是要說明如何啟用 Zendesk 的應用程式整合。

###<a name="to-enable-the-application-integration-for-zendesk,-perform-the-following-steps:"></a>若要啟用 Zendesk 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory] 。

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。

    ![應用程式](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Add an application from gallerry")

6.  在**搜尋方塊**中，輸入 **Zendesk**。

    ![應用程式庫](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Application Gallery")

7.  在結果窗格中，選取 [Zendesk]，然後按一下 [完成] 以新增應用程式。

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Zendesk。  
設定 Zendesk 的單一登入需要您從憑證抓取憑證指紋值。  
如果您不熟悉這個程序，請參閱 [如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 **Zendesk** 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。

    ![單一登入](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Single sign-on")

2.  在 [您希望使用者如何登入 Zendesk] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configure single sign-on")

3.  在 [設定應用程式 URL]  頁面上，執行下列步驟：

    ![設定應用程式 URL](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configure app URL")
  
    a. 在 [Zendesk 登入 URL] 文字方塊中，使用下列模式輸入您的 URL：`https://<tenant-name>.zendesk.com`

    b. 按 [下一步]。



4.  在 [設定在 Zendesk 單一登入] 頁面上，按一下 [下載憑證]，然後將憑證檔案儲存在本機電腦上。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configure single sign-on")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Zendesk 公司網站。

6.  按一下 Admin 。

7.  在左側導覽窗格中按一下 [設定]，然後按一下 [安全性]。

    ![安全性](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Security")

8.  在 [安全性] 頁面上，按一下 [系統管理員和代理程式] 索引標籤。

9.  選取 [單一登入 (SSO) 和 SAML]，然後選取 [SAML]。

10. 在 Azure 入口網站的 [設定在 Zendesk 單一登入] 頁面上，複製 [SAML SSO URL] 值，然後將它貼入 [SAML SSO URL] 文字方塊。

11. 在 Azure AD 入口網站的 [設定在 Zendesk 單一登入] 頁面上，複製 [遠端登出 URL] 值，然後將它貼入 [遠端登出 URL] 文字方塊。

    ![單一登入](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Single sign-on")

12. 從匯出的憑證複製 [指紋] 值，然後將它貼入 [憑證指紋] 文字方塊。

    >[AZURE.TIP] 如需詳細資訊，請參閱 [如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)

13. 按一下 [儲存] 。

14. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configure single sign-on")

##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
為了讓 Azure AD 使用者登入 **Zendesk**，他們必須佈建到 **Zendesk** 中。  
**Zendesk**需以手動的方式佈建。

###<a name="to-provision-a-user-account-to-zendesk,-perform-the-following-steps:"></a>若要佈建使用者帳戶到 Zendesk，請執行下列步驟：

1.  登入您的 **Zendesk** 租用戶。

2.  選取 [客戶清單]  索引標籤。

3.  選取 [使用者] 索引標籤，然後按一下 [新增]。

    ![新增使用者](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Add user")

4.  輸入您要佈建之現有 Azure AD 帳戶的電子郵件地址，然後按一下 [儲存] 。

    ![新增使用者](./media/active-directory-saas-zendesk-tutorial/IC773633.png "New user")

>[AZURE.NOTE] 您可以使用任何其他的 Zendesk 使用者帳戶建立工具或 Zendesk 提供的 API，佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###<a name="to-assign-users-to-zendesk,-perform-the-following-steps:"></a>若要指派使用者給 Zendesk，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Zendesk** 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assign users")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。



<!--HONumber=Oct16_HO2-->


