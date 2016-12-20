---
title: "教學課程：Azure Active Directory 與 Work.com 整合 | Microsoft Docs"
description: "了解如何使用 Work.com 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5159115742ffda1951b0489864764065a6a3bec5


---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>教學課程：Azure Active Directory 與 Work.com 整合
本教學課程的目的是要示範 Azure 與 Work.com 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* 已啟用 Work.com 單一登入功能的訂用帳戶

完成本教學課程之後，或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)，指派了 Work.com 存取的 AAD 使用者就能夠在 Work.com 公司網站 (服務提供者起始登入) 的應用程式單一登入。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Work.com 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenario")

## <a name="enabling-the-application-integration-for-workcom"></a>啟用 Work.com 的應用程式整合
本節的目的是概述如何啟用 Work.com 的應用程式整合。

### <a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>若要啟用 Work.com 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-work-com-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-work-com-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **Work.com**。
   
   ![應用程式庫](./media/active-directory-saas-work-com-tutorial/IC794106.png "Application Gallery")
7. 在結果窗格中，選取 [Work.com]，然後按一下 [完成] 加入應用程式。
   
   ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

## <a name="configuring-single-sign-on"></a>設定單一登入
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Work.com 中進行驗證。  
您必須將憑證上傳至 Work.com.com，這是程序的一部分。

> [!NOTE]
> 若要設定單一登入，您還需要設定自訂的 Work.com 網域名稱。 您至少需要定義一個網域名稱、測試網域名稱，並將它部署到整個組織。
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 以系統管理員身分登入 Work.com 租用戶。
2. 移到 [設定] 。
   
   ![設定](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
3. 在 [系統管理員] 區段的左方導覽窗格中，按一下 [網域管理] 展開相關的區段，然後按一下 [我的網域] 來開啟 [我的網域] 頁面。 
   
   ![我的網域](./media/active-directory-saas-work-com-tutorial/IC767825.png "My Domain")
4. 若要確認是否已正確設定您的網域，請確定它在 [步驟 4 已部署到使用者的網域] 中，並檢閱 [我的網域設定]。
   
   ![已部署到使用者的網域](./media/active-directory-saas-work-com-tutorial/IC784377.png "Doman Deployed to User")
5. 在不同的 Web 瀏覽器視窗中，登入您的 Azure 傳統入口網站。
6. 在 [Work.com] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configure Single Sign-On")
7. 在 [要如何讓使用者登入 Work.com] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configure Single Sign-On")
8. 在 [設定應用程式 URL] 頁面的 [Work.com 登入 URL] 文字方塊中，輸入使用者用來登入 Work.com 應用程式的 URL (例如：” *http://company.my.salesforce.com*”)，然後按 [下一步]： 
   
   ![設定應用程式 URL](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configure App URL")
9. 在 [設定在 Work.com 單一登入] 頁面上，若要下載您的憑證，請按一下 [下載憑證]，然後將憑證檔案儲存在本機電腦上。
   
   ![設定單一登入](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configure Single Sign-On")
10. 登入 Work.com 租用戶。
11. 移到 [設定] 。
    
    ![設定](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
12. 展開 [安全性控制] 功能表，然後再按一下 [單一登入設定]。
    
    ![單一登入設定](./media/active-directory-saas-work-com-tutorial/IC794113.png "Single Sign-On Settings")
13. 在 [單一登入設定]  對話方塊頁面執行下列步驟：
    
    ![啟用 SAML](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML Enabled")
    
    1. 選取 [已啟用 SAML] 。
    2. 按一下 [新增] 。
14. 在 [SAML 單一登入設定]  區段中，執行下列步驟：
    
    ![SAML 單一登入設定](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML Single Sign-On Setting")
    
    1. 在 [名稱]  文字方塊中，輸入您的組態名稱。  
       
       > [!NOTE]
       > 提供 [名稱] 的值，[API 名稱] 文字方塊就會自動填入內容。
       > 
       > 
    2. 在 Azure 傳統入口網站中的 [設定在 Work.com 單一登入] 對話方塊頁面上，複製 [簽發者 URL] 值，然後將它貼到 [簽發者] 文字方塊中。
    3. 若要上傳已下載的憑證，請按一下 [瀏覽] 。
    4. 在 [實體識別碼] 文字方塊中，輸入 **https://salesforce-work.com**。
    5. 對於 [SAML 身分識別類型]，選取 [判斷提示包含來自使用者物件的同盟識別碼]。
    6. 在 [SAML 識別位置]，請選取 [識別位於 Subject 陳述式的 NameIdentifier 元素中]。
    7. 在 Azure 傳統入口網站中的 [設定在 Work.com 單一登入] 對話方塊頁面上，複製 [遠端登入 URL] 值，然後將它貼到 [識別提供者登入 URL] 文字方塊中。
    8. 在 Azure 傳統入口網站中的 [設定在 Work.com 單一登入] 對話方塊頁面上，複製 [遠端登出 URL] 值，然後將它貼到 [識別提供者登出 URL] 文字方塊中。
    9. 在 [服務提供者起始的要求繫結]，選取 [HTTP Post]。
    10. 按一下 [儲存] 。
15. 在 Work.com 傳統入口網站的左側導覽窗格中，按一下 [網域管理] 以展開相關區段，然後按一下 [我的網域] 來開啟 [我的網域] 頁面。 
    
    ![我的網域](./media/active-directory-saas-work-com-tutorial/IC794115.png "My Domain")
16. 在 [我的網域] 頁面的 [登入頁面商標] 區段中，按一下 [編輯]。
    
    ![登入頁面商標](./media/active-directory-saas-work-com-tutorial/IC767826.png "Login Page Branding")
17. [登入頁面商標] 頁面的 [驗證服務] 區段中，會顯示您的 [SAML SSO 設定] 的名稱。 請選取該名稱，然後按一下 [儲存] 。
    
    ![登入頁面商標](./media/active-directory-saas-work-com-tutorial/IC784366.png "Login Page Branding")
18. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
    
    ![設定單一登入](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>設定使用者佈建
Azure Active Directory 使用者必須先佈建到 Work.com，才可以登入。  
Work.com 需以手動方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身分登入您的 Work.com 公司網站。
2. 移到 [設定] 。
   
   ![設定](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
3. 移至 [管理使用者 \> 使用者]。
   
   ![管理使用者](./media/active-directory-saas-work-com-tutorial/IC784369.png "Manage Users")
4. 按一下 [新使用者] 。
   
   ![所有使用者](./media/active-directory-saas-work-com-tutorial/IC794117.png "All Users")
5. 在 [使用者編輯] 區段中，執行下列步驟：
   
   ![使用者編輯](./media/active-directory-saas-work-com-tutorial/IC794118.png "User Edit")
   
   1. 輸入要佈建到相關文字方塊的有效 Azure Active Directory 帳戶的 [姓氏]、[別名]、[電子郵件]、[使用者名稱] 和 [暱稱] 屬性。
   2. 選取 [角色]、[使用者授權] 和 [設定檔]。
   3. 按一下 [儲存] 。  
      
      > [!NOTE]
      > Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以便在帳戶啟用前加以確認。
      > 
      > 

> [!NOTE]
> 您可以使用任何其他的 Work.com 使用者帳戶建立工具或 Work.com 提供的 API 來佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-workcom-perform-the-following-steps"></a>若要將使用者指派到 Work.com，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Work.com] 應用程式整合頁面上，按一下 [指派使用者] 。
   
   ![指派使用者](./media/active-directory-saas-work-com-tutorial/IC794119.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![yes](./media/active-directory-saas-work-com-tutorial/IC767830.png "Yes")

請等候 10 分鐘並確認帳戶已同步處理至 Work.com.com。

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


