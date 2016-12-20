---
title: "教學課程：Azure Active Directory 與 Brightspace by Desire2Learn 整合 | Microsoft Docs"
description: "了解如何使用 Brightspace by Desire2Learn 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b68e1f772071678cef5380bbcb7c2b057748a6f3


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>教學課程：Azure Active Directory 與 Brightspace by Desire2Learn 整合
本教學課程的目的是要示範 Azure 與 Brightspace by Desire2Learn 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 啟用 Brightspace by Desire2Learn 單一登入的訂用帳戶

完成本教學課程之後，您指派給 Brightspace by Desire2Learn 的 Azure AD 使用者就能夠單一登入您 Brightspace by Desire2Learn 公司網站 (服務提供者起始登入) 的應用程式，或是使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Brightspace by Desire2Learn 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scenario")

## <a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>啟用 Brightspace by Desire2Learn 的應用程式整合
本節的目的是要說明如何啟用 Brightspace by Desire2Learn 的應用程式整合。

### <a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>若要啟用 Brightspace by Desire2Learn 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **Brightspace by Desire2Learn**。
   
   ![應用程式庫](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Apllication Gallery")
7. 在結果窗格中，選取 [Brightspace by Desire2Learn]，然後按一下 [完成] 以加入應用程式。
   
   ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Brightspace by Desire2Learn。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [Brightspace by Desire2Learn] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configure Single Sign-On")
2. 在 [要如何讓使用者登入 Brightspace by Desire2Learn] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configure Single Sign-On")
3. 在 [設定應用程式 URL]  頁面上，執行下列步驟：
   
   ![設定應用程式 URL](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configure App URL")
   
   1. 在 [登入 URL] 文字方塊中，輸入使用者用來登入 **Brightspace by Desire2Learn**的 URL (例如︰*https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*)。
   2. 依序按一下 [ **下一步**
4. 於 [在 Brightspace by Desire2Learn 設定單一登入] 頁面上，按 [下載中繼資料] 以下載您的中繼資料，然後將中繼資料儲存在您的電腦中。
   
   ![設定單一登入](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configure Single Sign-On")
5. 將下載的中繼資料檔傳送給 Brightspace by Desire2Learn 支援小組。
   
   > [!NOTE]
   > Brightspace by Desire2Learn 支援小組必須執行實際的 SSO 組態。
   > 當您的訂用帳戶啟用 SSO 之後，您會收到通知。
   > 
   > 
6. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者可以登入 Brightspace by Desire2Learn，必須將他們佈建到 Brightspace by Desire2Learn。  
Brightspace by Desire2Learn 的使用者帳戶必須由您的 Brightspace by Desire2Learn 支援小組建立。

> [!NOTE]
> 您可以使用任何其他的 Brightspace by Desire2Learn 使用者帳戶建立工具或 Brightspace by Desire2Learn 提供的 API 來佈建 Azure Active Directory 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>若要指派使用者給 Brightspace by Desire2Learn，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Brightspace by Desire2Learn] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


