---
title: "教學課程：Azure Active Directory 與 15Five 整合 | Microsoft Docs"
description: "了解如何使用 15Five 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: ce98338e6b21eb35a17f0183f409dd54d1123bb9


---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>教學課程：Azure Active Directory 與 15Five 整合
本教學課程的目的是要示範 Azure 與 15Five 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 啟用 15Five 單一登入的訂用帳戶

完成本教學課程之後，您指派給 15Five 的 Azure AD 使用者就能夠單一登入您 15Five 公司網站 (服務提供者起始登入) 的應用程式，或是使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)進行單一登入。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 15Five 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-15five-tutorial/IC784667.png "Scenario")

## <a name="enabling-the-application-integration-for-15five"></a>啟用 15Five 的應用程式整合
本節的目的是概述如何啟用 15Five 的應用程式整合。

### <a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>若要啟用 15Five 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-15five-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-15five-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-15five-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **15Five**。
   
   ![應用程式庫](./media/active-directory-saas-15five-tutorial/IC784668.png "Application Gallery")
7. 在結果窗格中，選取 [15Five]，然後按一下 [完成] 加入應用程式。
   
   ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
   
## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶對 15Five 進行驗證。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [15Five] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784670.png "Configure single sign-on")
2. 在 [要如何讓使用者登入 15Five] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784671.png "Configure single sign-on")
3. 在 [設定應用程式 URL] 頁面的 [15Five 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://company.15Five.com*"，然後按一下 [下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-15five-tutorial/IC784672.png "Configure App URL")
4. 在 [設定在 15Five 單一登入] 頁面上，按一下 [下載中繼資料]，然後將中繼資料檔轉寄給 15Five 支援小組。
   
   ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784673.png "Configure single sign-on")
   
   > [!NOTE]
   > 單一登入必須由 15Five 支援小組啟用。
   > 
   > 
5. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784674.png "Configure single sign-on")
   

## <a name="configuring-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者能夠登入 15Five，必須將他們佈建到 15Five。  
15Five 需以手動方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身分登入您的 **15Five** 公司網站。
2. 移至 [管理公司] 。
   
   ![管理公司](./media/active-directory-saas-15five-tutorial/IC784675.png "Manage Company")
3. 移至 [人員]\>[新增人員]。
   
   ![人員](./media/active-directory-saas-15five-tutorial/IC784676.png "People")
4. 在 [新增人員] 區段中，執行下列步驟：
   
   ![新增人員](./media/active-directory-saas-15five-tutorial/IC784677.png "Add New Person")
   
   1. 在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的 [名字]、[姓氏]、[職稱]、[電子郵件地址]。
   2. 按一下 [完成] 。
   
   > [!NOTE]
   > Azure AD 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。
   > 
   > 



## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-15five-perform-the-following-steps"></a>若要將使用者指派到 15Five，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [15Five] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-15five-tutorial/IC784678.png "Assign users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-15five-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


