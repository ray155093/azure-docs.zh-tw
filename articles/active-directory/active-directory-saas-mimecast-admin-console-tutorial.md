---
title: "教學課程：Azure Active Directory 與 Mimecast Admin Console 整合 | Microsoft Docs"
description: "了解如何使用 Mimecast Admin Console 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7271ace1c6bd1d56e20106e09ba125ac59145d1d


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>教學課程：Azure Active Directory 與 Mimecast Admin Console 整合
本教學課程的目的是要示範 Azure 與 Mimecast Admin Console 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 啟用 Mimecast Admin Console 單一登入的訂用帳戶

完成本教學課程之後，您指派給 Mimecast Admin Console 的 Azure AD 使用者將能夠單一登入您 Mimecast Admin Console 公司網站 (服務提供者起始登入) 的應用程式，或是使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Mimecast Admin Console 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scenario")

## <a name="enabling-the-application-integration-for-mimecast-admin-console"></a>啟用 Mimecast Admin Console 的應用程式整合
本節的目的是要說明如何啟用 Mimecast Admin Console 的應用程式整合。

### <a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>若要啟用 Mimecast Admin Console 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **Mimecast Admin Console**。
   
   ![應用程式庫](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Application Gallery")
7. 在結果窗格中，選取 [Mimecast Admin Console]，然後按一下 [完成] 以新增應用程式。
   
   ![Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast Admin Console")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Mimecast Admin Console 中進行驗證。  
在此程序中，您必須建立 Base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [Mimecast Admin Console] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configure Single Sign-On")
2. 在 [您希望使用者如何登入 Mimecast Admin Console] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configure Single Sign-On")
3. 在 [設定應用程式 URL] 頁面的 [Mimecast Admin Console 登入 URL] 文字方塊中，輸入使用者登入您的 Mimecast Admin Console 應用程式所使用的 URL (如：“https://webmail-uk.mimecast.com” 或 “https://webmail-us.mimecast.com”)，然後按一下 [下一步]。
   
   > [!NOTE]
   > 登入 URL 是地區特定的 URL。
   > 
   > 
   
   ![設定應用程式 URL](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configure App URL")
4. 在 [設定在 Mimecast Admin Console 單一登入] 頁面上，按一下 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在您的本機電腦中。
   
   ![設定單一登入](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configure Single Sign-On")
5. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Mimecast Admin Console 公司網站。
6. 移至 [服務 \> 應用程式]。
   
   ![服務](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Services")
7. 按一下 [驗證設定檔] 。
   
   ![驗證設定檔](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Authentication Profiles")
8. 按一下 [新驗證設定檔] 。
   
   ![新驗證設定檔](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "New Authentication Profiles")
9. 在 [驗證設定檔]  區段中，執行下列步驟：
   
   ![驗證設定檔](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Authentication Profile")
   
   1. 在 [描述]  文字方塊中，輸入您的組態名稱。
   2. 選取 [強制執行 Mimecast Admin Console 的 SAML 驗證] 。
   3. 在 [提供者] 中選取 [Azure Active Directory]。
   4. 在 Azure 傳統入口網站中的 [設定在 Mimecast Admin Console 單一登入] 對話頁面上，複製 [簽發者 URL] 值，然後將它貼至 [簽發者 URL] 文字方塊中。
   5. 在 Azure 傳統入口網站中的 [設定在 Mimecast Admin Console 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [登入 URL] 文字方塊中。
   6. 在 Azure 傳統入口網站中的 [設定在 Mimecast Admin Console 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [登出 URL] 文字方塊中。  
      
      > [!NOTE]
      > Mimecast Admin Console 的登入 URL 值與登出 URL 值是相同的。
      > 
      > 
   7. 從您下載的憑證建立「Base-64 編碼」  檔案。  
      
      > [!TIP]
      > 如需詳細資訊，請參閱 [如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。
      > 
      > 
   8. 在記事本中開啟您的 base-64 編碼的憑證，移除第一行 (“*--*“) 與最後一行 (“*--*“)，將它的其餘內容複製到您的剪貼簿，然後貼到 [識別提供者憑證 (中繼資料)] 文字方塊中。
   9. 選取 [允許單一登入] 。
   10. 按一下 [儲存] 。
10. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
    
    ![設定單一登入](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Mimecast Admin Console，必須將它們佈建到 Mimecast Admin Console。  
Mimecast Admin Consol 需以手動的方式佈建。

您需要先註冊網域才能建立使用者。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身份登入您的 **Mimecast Admin Console** 。
2. 移至 [目錄 \> 內部]。
   
   ![目錄](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Directories")
3. 按一下 [註冊新網域] 。
   
   ![註冊新網域](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Register New Domain")
4. 在您建立好新網域之後，，按一下 [新位址] 。
   
   ![新位址](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "New Address")
5. 在 [新位址] 對話方塊中，執行下列步驟：
   
   ![儲存](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Save")
   
   1. 在相關的文字方塊中，輸入您要佈建之有效 AAD 帳戶的 [電子郵件地址]、[全域名稱]、[密碼]、[確認密碼] 屬性。
   2. 按一下 [儲存] 。

> [!NOTE]
> 您可以使用任何其他的 Mimecast Admin Console 使用者帳戶建立工具或 Mimecast Admin Console 提供的 API，佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### <a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>若要指派使用者給 Mimecast Admin Console，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Mimecast Admin Console] 應用程式整合頁面中，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


