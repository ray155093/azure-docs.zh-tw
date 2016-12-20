---
title: "教學課程：Azure Active Directory 與 IdeaScale 整合 | Microsoft Docs"
description: "了解如何使用 IdeaScale 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f5e9f3ae668cc1f70bb6c0b1abc0e6898f4aaa48


---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>教學課程：Azure Active Directory 與 IdeaScale 整合
本教學課程的目的是要示範 Azure 與 IdeaScale 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 已啟用 IdeaScale 單一登入的訂閱

完成本教學課程之後，您指派給 IdeaScale 的 Azure AD 使用者就能夠單一登入應用程式，或是使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 IdeaScale 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenario")

## <a name="enabling-the-application-integration-for-ideascale"></a>啟用 IdeaScale 的應用程式整合
本節的目的是要說明如何啟用 IdeaScale 的應用程式整合。

### <a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>若要啟用 IdeaScale 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **IdeaScale**。
   
   ![應用程式庫](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Application Gallery")
7. 在結果窗格中，選取 [IdeaScale]，然後按一下 [完成] 以新增應用程式。
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 IdeaScale 中進行驗證。  
設定 IdeaScale 的單一登入需要您從憑證抓取指紋值。  
如果您不熟悉這個程序，請參閱 [如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [IdeaScale] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configure Single Sign-On")
2. 在 [您希望使用者如何登入 IdeaScale] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configure Single Sign-On")
3. 在 [設定應用程式 URL] 頁面的 [IdeaScale 單一登入 URL] 文字方塊中，輸入使用者用來登入 IdeaScale 應用程式的 URL (例如："*https://company.IdeaScale.com*")，然後按一下[下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configure App URL")
4. 在 [設定在 IdeaScale 單一登入] 頁面上，若要下載您的中繼資料，請按一下 [下載中繼資料]，然後將中繼資料檔案儲存在您的本機電腦中。
   
   ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configure Single Sign-On")
5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 IdeaScale 公司網站。
6. 移至 [社群設定] 。
   
   ![社群設定](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
7. 移至 [安全性] \> [單一登入設定]。
   
   ![單一登入設定](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single Signon Settings")
8. 在 [單一登入類型]，選取 [SAML 2.0]。
   
   ![單一登入類型](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")
9. 在 [單一登入設定]  對話方塊上執行下列步驟：
   
   ![單一登入設定](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single Signon Settings")
   
   1. 在 Azure 傳統入口網站中的 [設定在 IdeaScale 單一登入] 對話方塊頁面上，複製 [實體識別碼] 值，然後將它貼至 [SAML IdP 實體識別碼] 文字方塊中。
   2. 複製您下載的中繼資料檔案的內容，並將它貼到 [SAML IdP 中繼資料]  文字方塊。
   3. 在 Azure 傳統入口網站中的 [設定在 IdeaScale 單一登入] 對話方塊頁面上，複製 [遠端登出 URL] 值，然後將它貼至 [登出成功 URL] 文字方塊中。
   4. 按一下 [儲存變更] 。
10. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
    
    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者可以登入 IdeaScale，則必須將他們佈建到 IdeaScale。  
在 IdeaScale 的情況下，佈建是手動工作。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身分登入您的 **IdeaScale** 公司網站。
2. 移至 [社群設定] 。
   
   ![社群設定](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
3. 移至 [基本設定] \> [成員管理]。
4. 按一下 [新增成員] 。
   
   ![成員管理](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")
5. 在 [加入新成員] 對話方塊中，執行下列步驟：
   
   ![加入新成員](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")
   
   1. 在 [電子郵件地址]  文字方塊輸入您想要佈建之 AAD 帳戶的有效電子郵件地址。
   2. 按一下 [儲存變更] 。
   
   > [!NOTE]
   > Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。
   > 
   > 

> [!NOTE]
> 您可以使用任何其他的 IdeaScale 使用者帳戶建立工具或 IdeaScale 提供的 API，來佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### <a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>若要指派使用者給 IdeaScale，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [IdeaScale] 應用程式整合頁面中，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


