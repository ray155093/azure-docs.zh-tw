---
title: "教學課程：Azure Active Directory 與 xMatters OnDemand 整合 | Microsoft Docs"
description: "了解如何使用 xMatters OnDemand 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 143e4a856a31a44a9ec909d07d5f24710a6e2803


---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>教學課程：Azure Active Directory 與 xMatters OnDemand 整合
本教學課程的目的是要示範 Azure 與 xMatters OnDemand 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* XMatters OnDemand 租用戶

完成本教學課程之後，您指派給 xMatters OnDemand 的 Azure AD 使用者就能夠從您的 xMatters OnDemand 公司網站 (服務提供者起始登入)，或使用 [存取面板](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 xMatters OnDemand 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario")

## <a name="enabling-the-application-integration-for-xmatters-ondemand"></a>啟用 xMatters OnDemand 的應用程式整合
本節的目的是要說明如何啟用 xMatters OnDemand 的應用程式整合。

### <a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>若要啟用 xMatters OnDemand 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4. 按一下頁面底部的 [新增]  。
   
    ![新增應用程式](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Add application")

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![從組件庫新增應用程式](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Add an application from gallerry")

6. 在**搜尋方塊**中，輸入 **xMatters OnDemand**。
   
    ![應用程式庫](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Application gallery")

7. 在結果窗格中，選取 [xMatters OnDemand]，然後按一下 [完成] 以新增應用程式。
   
    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

## <a name="configuring-single-sign-on"></a>設定單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 xMatters OnDemand 中進行驗證。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [xMatters OnDemand] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configure single sign-on")

2. 在 [您希望使用者如何登入 xMatters OnDemand] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configure single sign-on")

3. 在 [設定應用程式 URL]  頁面上，執行下列步驟：
   
    ![設定應用程式 URL](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configure app URL")
   
    a. 在 [XMatters OnDemand 登入 URL] 文字方塊中，使用下列模式輸入您的 URL︰`https://<tenant-name>.XMattersOnDemandapp.com`
   
    b.這是另一個 C# 主控台應用程式。 按 [下一步] 。

4. 在 [設定在 xMatters OnDemand 單一登入] 頁面上，若要下載您的憑證，請按一下 [下載憑證]，然後將憑證檔案以 **c:\\XMatters OnDemand.cer** 方式儲存在本機。
   
    > [!IMPORTANT]
    > 您需要將憑證轉送給 xMatters 支援小組。 xMatters 支援小組需要先上傳憑證，您才能完成單一登入組態。
    > 
    > 
   
    ![設定單一登入](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configure single sign on")

5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 xMatters OnDemand 公司網站。

6. 在頂端工具列中按一下 [管理]，然後按一下左側導覽列中的 [公司詳細資料]。
   
    ![Admin](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7. 在 [SAML 組態]  頁面上，執行下列步驟：
   
    ![SAML 設定](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML configuration")
   
    a. 選取 [啟用 SAML] 。
   
    b.這是另一個 C# 主控台應用程式。 在 Azure 傳統入口網站的 [設定在 xMatters OnDemand 單一登入] 對話方塊頁面上，複製 [識別提供者 ID] 值，然後將它貼到 [識別提供者 ID] 文字方塊中。
   
    c. 在 Azure 傳統入口網站的 [設定在 xMatters OnDemand 單一登入] 對話方塊頁面上，複製 [單一登入服務 URL] 值，然後將它貼到 [單一登入 URL] 文字方塊中。
   
    d. 在 Azure 傳統入口網站的 [設定在 xMatters OnDemand 單一登入] 對話方塊頁面上，複製 [單一登出服務 URL] 值，然後將它貼到 [單一登出 URL] 文字方塊中。
   
    e. 在 [公司詳細資料] 頁面頂端，按一下 [儲存變更] 。
    
    ![公司詳細資料](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Company details")

8. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configure single sign on")

## <a name="configuring-user-provisioning"></a>設定使用者佈建
若要讓 Azure AD 使用者可以登入 xMatters OnDemand，則必須將他們佈建到 xMatters OnDemand。  
xMatters OnDemand 需以手動的方式佈建。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要佈建使用者帳戶，請執行下列步驟：
1. 登入 **xMatters OnDemand** 租用戶。

2. 按一下 [使用者]  索引標籤。

3. 按一下 [加入使用者] 。
  
    ![使用者](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Users")

4. 選取 [使用中] 。

5. 在 [加入使用者]  區段中，執行下列步驟：
   
    ![加入使用者](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Add a User")
   
    a. 輸入您想要佈建之有效 AAD 帳戶的 [使用者識別碼]、[名字]、[姓氏]、[網站]。
    
    b.這是另一個 C# 主控台應用程式。 按一下 [儲存] 。


## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>若要指派使用者給 xMatters OnDemand，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。

2. 在 [xMatters OnDemand] 應用程式整合頁面上，按一下 [指派使用者]。
   
    ![指派使用者](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assign users")

3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
    ![是](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Dec16_HO1-->


