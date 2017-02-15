---
title: "教學課程：Azure Active Directory 與 Salesforce 沙箱整合 | Microsoft Docs"
description: "了解如何使用 Salesforce 沙箱搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 286f5a31a410914b32dd407055d8e0abe4c6eeda


---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>教學課程：Azure Active Directory 與 Salesforce 沙箱整合
> [!TIP]
> 如需意見反應，請按一下 [這裡](http://go.microsoft.com/fwlink/?LinkId=521878)。
> 
> 

本教學課程的目的是要示範 Azure 與 Salesforce 沙箱的整合。  
沙箱讓您能夠針對不同用途 (例如開發、測試和訓練) 在個別環境中建立貴組織的多個複本，而不會危害 Salesforce 生產環境組織中的資料和應用程式。  
如需詳細資訊，請參閱 [沙箱概觀](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)

本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 在 Salesforce.com 中的沙箱

如果您在 Salesforce.com 中還沒有有效的沙箱，則您需要連絡 Salesforce。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Salesforce 沙箱的應用程式整合
2. 設定單一登入
3. 啟用您的網域
4. 設定使用者佈建
5. 指派使用者

![案例](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scenario")

## <a name="enabling-the-application-integration-for-salesforce-sandbox"></a>啟用 Salesforce 沙箱的應用程式整合
本節的目的是要說明如何啟用 Salesforce 沙箱的應用程式整合。

### <a name="to-enable-the-application-integration-for-salesforce-sandbox-perform-the-following-steps"></a>若要啟用 Salesforce 沙箱的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applications")
4. 若要開啟 [應用程式庫]，請按一下 [新增應用程式]，然後按一下 [新增應用程式讓我的組織使用]。
   
   ![欲執行動作](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "What do you want to do?")
5. 在 [搜尋方塊] 中，輸入 **Salesforce 沙箱**。
   
   ![應用程式庫](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Application Gallery")
6. 在結果窗格中，選取 [Salesforce 沙箱]，然後按一下 [完成] 來新增應用程式。
   
   ![Salesforce 沙箱](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Salesforce 中進行驗證。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [Salesforce 沙箱] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configure single sign-on")
2. 在 [要如何讓使用者登入 Salesforce 沙箱] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![Salesforce 沙箱](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")
3. 在 [設定應用程式 URL] 頁面的 [登入 URL] 文字方塊中，使用下列模式輸入您的 URL：`http://company.my.salesforce.com`，然後按 [下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configure App URL")
4. 如果您已為目錄中的另一個 Salesforce 沙箱執行個體設定單一登入，則也須將**識別碼**設定為具有與**登入 URL** 相同的值。 您也可以在對話方塊的 [設定應用程式 URL] 頁面上核取 [顯示進階設定] 核取方塊，來尋找 [識別碼] 欄位。
5. 在 [設定在 Salesforce 沙箱單一登入] 頁面上，按一下 [下載憑證]，然後將憑證檔案儲存在您的電腦上。
   
   ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configure Single Sign-On")
6. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Salesforce 沙箱。
7. 在頂端的功能表中，按一下 [安裝] 。
   
   ![設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Setup")
8. 在左側的導覽窗格中，按一下 [安全性控制項]，然後按一下 [單一登入設定]。
   
   ![單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Single Sign-On Settings")
9. 在 [單一登入設定] 區段中，執行下列步驟：
   
   ![單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Single Sign-On Settings")
   
   a.  選取 [已啟用 SAML] 。
   
   b.這是另一個 C# 主控台應用程式。  按一下 [新增] 。
10. 在 [SAML 單一登入設定] 區段中，執行下列步驟：
    
    ![SAML 單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML Single Sign-On Settings")
    
    a.  在 [名稱] 文字方塊中，輸入組態的名稱 (例如：*SPSSOWAAD\_Test*)。
    
    b.  在 Azure 傳統入口網站中的 [設定在 Salesforce 沙箱單一登入] 對話方塊頁面上，複製 [簽發者 URL] 值，然後貼到 [簽發者] 文字方塊中。
    
    c.  如果這是您要新增至目錄的第一個 Salesforce 沙箱執行個體，請在 [實體識別碼] 文字方塊中，輸入 **https://test.salesforce.com**。 如果您已新增 Salesforce 沙箱的執行個體，請對 [實體識別碼] 輸入**登入 URL**，其格式如下：`http://company.my.salesforce.com`
    
    d.  按一下 [瀏覽] 來上傳已下載的憑證。
    
    e.  對於 [SAML 身分識別類型]，選取 [判斷提示包含來自使用者物件的同盟識別碼]。
    
    f.  對於 [SAML 身分識別位置]，選取 [身分識別位於 Subject 陳述式的 NameIdentifier 元素中]。
    
    g.  在 Azure 傳統入口網站中的 [設定在 Salesforce 沙箱單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後貼到 [識別提供者登入 URL] 文字方塊中。
    
    h.  SFDC 不支援 SAML 登出。  解決方法是在 [識別提供者登出 URL] 文字方塊中貼上 'https://login.windows.net/common/wsfederation?wa=wsignout1.0'。
    
    i.  在 [服務提供者起始的要求繫結]，選取 [HTTP POST]。
    
    j. 按一下 [儲存] 。
11. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
    
    ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configure Single Sign-On")

## <a name="enabling-your-domain"></a>啟用您的網域
本節假設您已經建立了一個網域。  
如需詳細資訊，請參閱 [定義您的網域名稱](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。

### <a name="to-enable-your-domain-perform-the-following-steps"></a>若要啟用您的網域，請執行下列步驟：
1. 在左邊的導覽窗格中按一下 [網域管理]，然後按一下 [我的網域]。
   
   ![我的網域](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "My Domain")
   
   > [!NOTE]
   > 請確定您的網域已正確設定。
   > 
   > 
2. 在 [登入頁面設定] 區段中，按一下 [編輯]，然後對於 [驗證服務]，選取來自前一區段 SAML 單一登入設定的名稱，最後按一下 [儲存]。
   
   ![我的網域](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "My Domain")

一旦您設定了網域，您的使用者便應使用該網域 URL 登入至 Salesforce 沙箱。  
若要取得 URL 的值，請按一下您在上一區段中所建立的 SSO 設定檔。

## <a name="configuring-user-provisioning"></a>設定使用者佈建
本節的目的是要說明如何對 Salesforce 沙箱啟用 Active Directory 使用者帳戶的使用者佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 在 Salesforce 入口網站上方的導覽列中選取您的名稱來展開使用者功能表：
   
   ![我的設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "My Settings")
2. 從使用者功能表中，選取 [我的設定] 來開啟 [我的設定] 頁面。
3. 在左方導覽窗格中，按一下 [個人] 來展開 [個人] 區段，然後按一下 [重設我的安全性權杖]：
   
   ![我的設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "My Settings")
4. 在 [重設我的安全性權杖] 頁面上，按一下 [重設安全性權杖] 來要求包含 Salesforce.com 安全性權杖的電子郵件。
   
   ![新的權杖](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "New Token")
5. 檢查您的電子郵件收件匣，尋找來自 Salesforce.com 且主旨為「**salesforce.com.com 安全性確認**」的電子郵件。
6. 檢閱這封電子郵件並複製安全性權杖值。
7. 在 Azure 傳統入口網站中的 [Salesforce 沙箱] 應用程式整合頁面上，按一下 [設定使用者佈建] 來開啟 [設定使用者佈建] 對話方塊。
   
   ![設定使用者佈建](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configure user provisioning")
8. 在 [輸入您的 Salesforce 沙箱認證來啟用自動使用者佈建]  頁面上，提供以下組態設定：
   
   ![Salesforce 沙箱](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")
   
   a.  在 [Salesforce 沙箱管理員使用者名稱] 文字方塊中，輸入已在 Salesforce.com 中指派**系統管理員**設定檔的 Salesforce 沙箱帳戶名稱。
   
   b.  在 [Salesforce 沙箱管理員密碼] 文字方塊中，輸入這個帳戶的密碼。
   
   c.  在 [使用者安全性權杖] 文字方塊中，貼上安全性權杖值。
   
   d.  按一下 [驗證] 來驗證您的組態。
   
   e.  按 [下一步] 按鈕以開啟 [確認] 頁面。
9. 在 [確認] 頁面上，按一下 [完成] 來儲存您的組態。
   
   ## <a name="assigning-users"></a>指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-salesforce-sandbox-perform-the-following-steps"></a>若要將使用者指派給 Salesforce 沙箱，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Salesforce 沙箱] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Assign users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![yes](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Yes")

請等候 10 分鐘並確認帳戶已同步至 Salesforce 沙箱。

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](https://msdn.microsoft.com/library/dn308586)。




<!--HONumber=Nov16_HO3-->


