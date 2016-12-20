---
title: "教學課程：Azure Active Directory 與 AppDynamics 整合 | Microsoft Docs"
description: "了解如何使用 AppDynamics 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13f4e780afc26dc0f1f59408a77f68a7e46308c7


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>教學課程：Azure Active Directory 與 AppDynamics 整合
本教學課程的目的是要示範 Azure 與 AppDynamics 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 啟用 AppDynamics 單一登入的訂用帳戶

完成本教學課程之後，您指派給 AppDynamics 的 Azure AD 使用者就能夠單一登入您 AppDynamics 公司網站 (服務提供者起始登入) 的應用程式，或是使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 AppDynamics 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")

## <a name="enabling-the-application-integration-for-appdynamics"></a>啟用 AppDynamics 的應用程式整合
本節的目的是要說明如何啟用 AppDynamics 的應用程式整合。

### <a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>若要啟用 AppDynamics 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **AppDynamics**。
   
   ![應用程式庫](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")
7. 在結果窗格中，選取 [AppDynamics]，然後按一下 [完成] 以加入應用程式。
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 AppDynamics。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [AppDynamics] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")
2. 在 [要如何讓使用者登入 AppDynamics] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")
3. 在 [設定應用程式 URL] 頁面的 [AppDynamics 登入 URL] 文字方塊中，輸入您的使用者用來登入 AppDynamics 的 URL ("*https://companyname.saas.appdynamics.com*")，然後按一下 [下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")
4. 於 [在 AppDynamics 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。
   
   ![設定單一登入](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")
5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 AppDynamics 公司網站。
6. 在頂端工具列中，按一下 [設定]，然後按一下 [管理]。
   
   ![系統管理](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")
7. 按一下 [驗證提供者]  索引標籤。
   
   ![驗證提供者](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")
8. 在 [驗證提供者]  區段中，執行下列步驟：
   
   ![SAML 設定](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")
   
   1. 針對 [驗證提供者]，選取 [SAML]。
   2. 在 Azure 傳統入口網站的 [在 AppDynamics 設定單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [登入 URL] 文字方塊中。
   3. 在 Azure 傳統入口網站的 [在 AppDynamics 設定單一登入] 對話頁面上，複製 [遠端登出 URL] 值，然後將它貼至 [登出 URL] 文字方塊中。
   4. 從您下載的憑證建立「Base-64 編碼」  檔案。  
      
      > [!TIP]
      > 如需詳細資訊，請參閱 [如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. 在記事本中開啟您的 Base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [憑證]  文字方塊中。
   6. 按一下 [儲存] 。
      ![儲存](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")
9. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者可以登入 AppDynamics，必須將他們佈建到 AppDynamics。  
AppDynamics 需以手動方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身分登入您的 AppDynamics 公司網站。
2. 移至 [使用者]，然後按一下 **+** 開啟 [建立使用者] 對話方塊。
   
   ![使用者](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")
3. 在 [建立使用者]  區段中，執行下列步驟：
   
   ![建立使用者](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")
   
   1. 在相關的文字方塊中，輸入您要佈建之有效 AAD 帳戶的 [使用者名稱]、[名稱]、[電子郵件]、[新密碼]、[重複新密碼]。
   2. 按一下 [儲存] 。

> [!NOTE]
> 您可以使用任何其他的 AppDynamics 使用者帳戶建立工具或 AppDynamics 提供的 API 來佈建 Azure AD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>若要指派使用者給 AppDynamics，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [AppDynamics] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


