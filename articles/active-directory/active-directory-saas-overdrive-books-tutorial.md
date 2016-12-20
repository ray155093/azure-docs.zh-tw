---
title: "教學課程：Azure Active Directory 與 Overdrive Books 整合 | Microsoft Docs"
description: "了解如何使用 Overdrive Books 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e68cede7-1130-4813-bd55-22a9a6fc6bf5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 66b3bbe84f966c3f8ec782fbf7c3bb20a8a832d0


---
# <a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>教學課程：Azure Active Directory 與 Overdrive Books 整合
本教學課程的目的是要示範 Azure 與 OverDrive 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 啟用 OverDrive 單一登入的訂閱

完成本教學課程之後，您指派給 OverDrive 的 Azure AD 使用者就能夠單一登入您 OverDrive 公司網站 (服務提供者起始登入) 的應用程式，或是使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 OverDrive 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scenario")

## <a name="enabling-the-application-integration-for-overdrive"></a>啟用 OverDrive 的應用程式整合
本節的目的是要說明如何啟用 OverDrive 的應用程式整合。

### <a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>若要啟用 OverDrive 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中輸入 **OverDrive**。
   
   ![應用程式庫](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Application Gallery")
7. 在結果窗格中，選取 [OverDrive]，然後按一下 [完成] 以新增應用程式。
   
   ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 OverDrive 中進行驗證。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [OverDrive] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![啟用單一登入](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Enable single sign-on")
2. 在 [要如何讓使用者登入 OverDrive] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configure single sign-on")
3. 在 [設定應用程式 URL] 頁面的 [OverDrive 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*http://mslibrarytest.libraryreserve.com*"，然後按一下 [下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configure App URL")
4. 在 [設定在 OverDrive 單一登入]  頁面上，下載中繼資料檔案，然後將檔案傳送到 OverDrive 支援小組。
   
   ![設定單一登入](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configure single sign-on")
   
   > [!NOTE]
   > OverDrive 支援小組會為您設定單一登入，並在完成設定時傳送通知給您。
   > 
   > 
5. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>設定使用者佈建

沒有動作項目可讓您設定 OverDrive 使用者佈建。  
當受指派使用者嘗試登入 OverDrive 時，則會自動建立一個 OverDrive 帳戶 (如有必要)。

> [!NOTE]
> 您可以使用任何其他的 OverDrive 使用者帳戶建立工具或 OverDrive 提供的 API 來佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

### <a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>若要將使用者指派給 OverDrive，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [OverDrive] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


