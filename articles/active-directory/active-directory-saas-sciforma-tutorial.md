---
title: "教學課程：Azure Active Directory 與 Sciforma 整合 | Microsoft Docs"
description: "了解如何使用 Sciforma 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: abbfb5ac-7687-4153-b263-8090102dae37
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a61552f3275aa58aca9c014d24ae69d91ce59d5


---
# <a name="tutorial-azure-ad-integration-with-sciforma"></a>教學課程：Azure AD 與 Sciforma 整合
本教學課程的目的是要示範 Azure 與 Sciforma 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* Sciforma 租用戶

完成本教學課程之後，您指派給 Sciforma 的 Azure AD 使用者就能夠從您的 Sciforma 公司網站 (服務提供者起始登入)，或使用 [存取面板](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Sciforma 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")

## <a name="enabling-the-application-integration-for-sciforma"></a>啟用 Sciforma 的應用程式整合
本節的目的是要說明如何啟用 Sciforma 的應用程式整合。

### <a name="to-enable-the-application-integration-for-sciforma-perform-the-following-steps"></a>若要啟用 Sciforma 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Add an application from gallerry")
6. 在 [搜尋方塊]中，輸入 **Sciforma**。
   
   ![應用程式庫](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Application gallery")
7. 在結果窗格中，選取 [Sciforma]，然後按一下 [完成] 來新增應用程式。
   
   ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Sciforma。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [Sciforma] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configure single sign-on")
2. 在 [要如何讓使用者登入 Sciforma] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configure single sign-on")
3. 在 [設定應用程式 URL] 頁面的 [Sciforma 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："https://\<tenant-name\>.Sciforma.com"，然後按 [下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configure app URL")
4. 在 [設定在 Sciforma 單一登入] 頁面上，按一下 [下載中繼資料] 來下載您的中繼資料，然後在您的本機電腦上將資料檔儲存為 **c:\\SciformaMetaData.xml**。
   
   ![設定單一登入](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configure single sign-on")
5. 將該中繼資料檔轉寄給 Sciforma 支援小組。 支援小組需要為您設定單一登入。
6. 選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>設定使用者佈建

沒有動作項目可讓您設定 Sciforma 使用者佈建。  
當指派的使用者透過存取面板嘗試登入 Sciforma 時，Sciforma 會檢查使用者是否存在。  
如果尚無可用的使用者帳戶，Sciforma 會自動予以建立。

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-sciforma-perform-the-following-steps"></a>若要將使用者指派給 Sciforma，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Sciforma] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assign users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


