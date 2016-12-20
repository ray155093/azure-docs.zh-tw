---
title: "教學課程：Azure Active Directory 與 InsideView 整合 | Microsoft Docs"
description: "了解如何使用 InsideView 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0da7c8f20fb6bbe78294aab2fb69c24a2dddd877


---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>教學課程：Azure Active Directory 與 InsideView 整合
本教學課程的目的是要示範 Azure 與 InsideView 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* InsideView 租用戶

完成本教學課程之後，您指派給 InsideView 的 Azure AD 使用者就能夠單一登入您 InsideView 公司網站 (服務提供者起始登入) 的應用程式，或是使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 InsideView 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario")

## <a name="enabling-the-application-integration-for-insideview"></a>啟用 InsideView 的應用程式整合
本節的目的是要說明如何啟用 InsideView 的應用程式整合。

### <a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>若要啟用 InsideView 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-insideview-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-insideview-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **InsideView**。
   
   ![應用程式庫](./media/active-directory-saas-insideview-tutorial/IC794129.png "Application Gallery")
7. 在結果窗格中，選取 [InsideView]，然後按一下 [完成] 以加入應用程式。
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 InsideView 中進行驗證。  
在此程序中，您必須建立 Base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [InsideView] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configure Single SignOn")
2. 在 [您希望使用者如何登入 InsideView] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configure Single SignOn")
3. 在 [設定應用程式 URL] 頁面的 [InsideView 回覆 URL] 文字方塊中，使用下列模式輸入您的 InsideView SSO URL (例如：`https://my.insideview.com/iv/<STS Name>/login.iv`)，然後按一下 [下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")
4. 在 [設定在 InsideView 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。
   
   ![設定單一登入](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configure Single SignOn")
5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 InsideView 公司網站。
6. 在頂端的工具列中，按一下 [系統管理員]，[單一登入設定設定]，然後按一下 [新增 SAML]。
   
   ![SAML 單一登入設定](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")
7. 在 [加入新的 SAML]  區段中，執行下列步驟：
   
   ![加入新的 SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")
   
   1. 在 [STS 名稱]  文字方塊中，輸入您的組態名稱。
   2. 在 Azure 傳統入口網站中的 [設定在 InsideView 單一登入] 對話方塊頁面，複製 [服務提供者 (SP) 啟始的端點] 值，然後將它貼至 [SamlP/WS-Fed 主動端點] 文字方塊中。
   3. 從您下載的憑證建立「Base-64 編碼」  檔案。
      
      > [!TIP]
      > 如需詳細資訊，請參閱 [如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. 在記事本中開啟您的 Base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後貼至 [STS 憑證]  文字方塊中。
   5. 在 [CRM 使用者識別碼對應] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
   6. 在 [CRM 電子郵件對應] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
   7. 在 [CRM 名字對應] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
   8. 在 [CRM 姓氏對應] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**。
   9. 按一下 [儲存] 。
8. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者可以登入 InsideView，則必須將他們佈建到 InsideView。  
在 InsideView 的情況下，佈建是手動工作。

若要在 InsideView 建立使用者或連絡人，請連絡您的 Customer Success Manager 或傳送電子郵件至 **support@insideview.com**

> [!NOTE]
> 您可以使用任何其他的 InsideView 使用者帳戶建立工具或 InsideView 提供的 API 來佈建 Azure AD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### <a name="to-assign-users-to-insideview-perform-the-following-steps"></a>若要指派使用者給 InsideView，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [InsideView] 應用程式整合頁面中，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-insideview-tutorial/IC794138.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


