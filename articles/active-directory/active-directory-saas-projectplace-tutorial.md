---
title: 教學課程：Azure Active Directory 與 Projectplace 整合 | Microsoft Docs
description: 了解如何使用 Projectplace 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-projectplace"></a>教學課程：Azure Active Directory 與 Projectplace 整合
本教學課程的目的是要示範 Azure 與 Projectplace 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* 啟用 Projectplace 單一登入的訂用帳戶

完成本教學課程之後，您已指派至 Projectplace 的 Azure AD 使用者就能夠從您的 Projectplace 公司網站 (服務提供者起始登入)，或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Projectplace 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")

## <a name="enabling-the-application-integration-for-projectplace"></a>啟用 Projectplace 的應用程式整合
本節的目的是要說明如何啟用 Projectplace 的應用程式整合。

### <a name="to-enable-the-application-integration-for-projectplace,-perform-the-following-steps:"></a>若要啟用 Projectplace 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **Projectplace**。
   
   ![應用程式庫](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Application Gallery")
7. 在結果窗格中，選取 [Projectplace]，然後按一下 [完成] 以新增應用程式。
   
   ![Projectplace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Projectplace 中進行驗證。

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [Projectplace] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configure Single SignOn")
2. 在 [要如何讓使用者登入 Projectplace] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configure Single SignOn")
3. 在 [設定應用程式 URL] 頁面的 [Projectplace 登入 URL] 文字方塊中，輸入您的 Projectplace 租用戶 URL (例如："http://company.projectplace.com")，然後按 [下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configure App URL")
4. 在 [設定在 Projectplace 單一登入] 頁面上，按一下 [下載中繼資料]，然後將中繼資料檔儲存在您的電腦中。
   
   ![設定單一登入](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configure Single SignOn")
5. 將中繼資料檔傳送給 Projectplace 支援小組。
   
   > [!NOTE]
   > 單一登入設定必須由 Projectplace 支援小組執行。 設定完成後，您將會收到通知。
   > 
   > 
6. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>設定使用者佈建

為了讓 Azure AD 使用者能夠登入 Projectplace，必須將他們佈建到 Projectplace。  
Projectplace 需以手動的方式佈建。

### <a name="to-provision-a-user-accounts,-perform-the-following-steps:"></a>若要佈建使用者帳戶，請執行下列步驟：
1. 以系統管理員身分登入您的 **Projectplace** 公司網站。
2. 移至 [人員]，然後按一下 [成員]。
   
   ![人員](./media/active-directory-saas-projectplace-tutorial/IC790228.png "People")
3. 按一下 [新增成員] 。
   
   ![新增成員](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Add Members")
4. 在 [新增成員]  區段中，執行下列步驟：
   
   ![新成員](./media/active-directory-saas-projectplace-tutorial/IC790233.png "New Members")
   
   1. 在 [新成員]  文字方塊中，於相關文字方塊中輸入您想要佈建之有效 AAD 帳戶的電子郵件地址。
   2. 按一下 [傳送] 
      
      > [!NOTE]
      > 系統會傳送一封電子郵件給 Azure Active Directory 帳戶的持有者，郵件中包含用來在啟用帳戶前確認帳戶的連結。
      > 
      > 

> [!NOTE]
> 您可以使用任何其他的 Projectplace 使用者帳戶建立工具或 Projectplace 提供的 API，佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-projectplace,-perform-the-following-steps:"></a>若要指派使用者給 Projectplace，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Projectplace] 應用程式整合頁面中，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

<!--HONumber=Oct16_HO2-->


