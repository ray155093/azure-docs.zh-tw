---
title: "教學課程：Azure Active Directory 與 Boomi 整合 | Microsoft Docs"
description: "了解如何使用 Boomi 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a315916e-8bfd-4390-bad2-ec9029314ab6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51ea1ededc7b850e3dff24c9f735789d34391434


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>教學課程：Azure Active Directory 與 Boomi 整合
本教學課程的目的是要示範 Azure 與 Boomi 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 啟用 Boomi 單一登入的訂用帳戶

完成本教學課程之後，您指派給 Boomi 的 Azure AD 使用者就能夠單一登入您 Boomi 公司網站 (服務提供者起始登入) 的應用程式，或是使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Boomi 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario")

## <a name="enabling-the-application-integration-for-boomi"></a>啟用 Boomi 的應用程式整合
本節的目的是要說明如何啟用 Boomi 的應用程式整合。

### <a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>若要啟用 Boomi 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-boomi-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-boomi-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜尋方塊**中，輸入 **Boomi**。
   
   ![應用程式庫](./media/active-directory-saas-boomi-tutorial/IC790822.png "Application Gallery")
7. 在結果窗格中，選取 [Boomi]，然後按一下 [完成] 以加入應用程式。
   
   ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
   
   ## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Boomi。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [Boomi] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configure Single Sign-On")
2. 在 [要如何讓使用者登入 Boomi] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configure Single Sign-On")
3. 在 [設定應用程式 URL] 頁面的 [Boomi 回覆 URL] 文字方塊中，輸入您的 [Boomi AtomSphere 登入 URL] (例如：“*https://platform.boomi.com/sso/AccountName/saml*”)，然後按一下 [下一步]。
   
   ![設定應用程式 URL](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configure App URL")
4. 在 [設定在 Boomi 單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後在本機電腦上儲存憑證檔案。
   
   ![設定單一登入](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configure Single Sign-On")
5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Boomi 公司網站。
6. 在頂端工具列中，依序按一下您的公司名稱和 [設定] 。
   
   ![設定](./media/active-directory-saas-boomi-tutorial/IC790828.png "Setup")
7. 按一下 [SSO 選項] 。
   
   ![SSO 選項](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")
8. 在 [單一登入選項]  區段中，執行下列步驟：
   
   ![單一登入選項](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")
   
   1. 選取 [啟用 SAML 單一登入] 。
   2. 按一下 [匯入] 來上傳已下載的憑證。
   3. 在 Azure 傳統入口網站的 [在 Boomi 設定單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [識別提供者登入 URL] 文字方塊中。
   4. 針對 [同盟識別碼位置]，選取 [同盟識別碼位於主旨的 NameID 項目中]。
   5. 按一下 [儲存] 。
9. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者可以登入 Boomi，必須將他們佈建到 Boomi。  
Boomi 需以手動方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身分登入您的 **Boomi** 公司網站。
2. 移至 [使用者管理] \> [使用者]。
   
   ![使用者](./media/active-directory-saas-boomi-tutorial/IC790831.png "Users")
3. 按一下 [新增使用者] 。
   
   ![新增使用者](./media/active-directory-saas-boomi-tutorial/IC790832.png "Add User")
4. 在 [加入使用者角色]  對話頁面上，執行下列步驟：
   
   ![新增使用者](./media/active-directory-saas-boomi-tutorial/IC790833.png "Add User")
   
   1. 在相關的文字方塊中，輸入您想要佈建之有效 AAD 帳戶的 [名字]、[姓氏] 和 [電子郵件]。
   2. 按一下 [確定]。

> [!NOTE]
> 您可以使用任何其他的 Boomi 使用者帳戶建立工具或 Boomi 提供的 API 來佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-boomi-perform-the-following-steps"></a>若要指派使用者給 Boomi，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Boomi] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-boomi-tutorial/IC790834.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-boomi-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


