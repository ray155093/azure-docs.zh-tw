---
title: "教學課程：Azure Active Directory 與 Veracode 整合 | Microsoft Docs"
description: "了解如何使用 Veracode 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 731a0fd05b6ed0c49c0c06836e33cd99f7e3bce2


---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>教學課程：Azure Active Directory 與 Veracode 整合
本教學課程的目的是要示範 Azure 與 Veracode 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* 已啟用 Veracode 單一登入功能的訂用帳戶

完成本教學課程之後，您指派給 Veracode 的 Azure AD 使用者就能夠從您的 Veracode 公司網站 (服務提供者起始登入)，或使用 [存取面板](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Veracode 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenario")

## <a name="enabling-the-application-integration-for-veracode"></a>啟用 Veracode 的應用程式整合
本節的目的是要說明如何啟用 Veracode 的應用程式整合。

### <a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>若要啟用 Veracode 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-veracode-tutorial/IC749321.png "Add application")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從組件庫新增應用程式](./media/active-directory-saas-veracode-tutorial/IC749322.png "Add an application from gallerry")
6. 在 [搜尋方塊] 中，輸入 **Veracode**。
   
   ![應用程式庫](./media/active-directory-saas-veracode-tutorial/IC802904.png "Application Gallery")
7. 在結果窗格中，選取 [Veracode]，然後按一下 [完成] 以加入應用程式。
   
   ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

## <a name="configuring-single-sign-on"></a>設定單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Veracode。  
Veracode 應用程式需要特定格式的 SAML 判斷提示，要求您加入自訂屬性對應到您的 **SAML 權杖屬性** 組態。  
以下螢幕擷取畫面顯示上述的範例。

![屬性](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [Veracode] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configure Single Sign-On")
2. 在 [要如何讓使用者登入 Veracode] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configure Single Sign-On")
3. 在 [設定應用程式設定] 頁面上，按 [下一步]。
   
   ![設定 App 設定](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configure App Settings")
4. 在 [設定在 Veracode 單一登入] 頁面上，若要下載您的憑證，請按一下 [下載憑證]，然後將憑證檔案儲存在本機電腦上。
   
   ![設定單一登入](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configure Single Sign-On")
5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Veracode 公司網站。
6. 在頂端功能表中，按一下 [設定]，然後按一下 [管理員]。
   
   ![系統管理](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")
7. 按一下 [SAML]  索引標籤。
8. 在 [組織 SAML 設定]  區段中，執行下列步驟：
   
   ![系統管理](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")
   
   1. 在 Azure 傳統入口網站的 [設定在 Veracode 單一登入] 對話方塊頁面上，複製 [簽發者 URL] 值，然後將它貼到 [簽發者] 文字方塊中
   2. 若要上傳您下載的憑證，請按一下 [選擇檔案] 。
   3. 選取 [啟用自動註冊] 。
9. 在 [自動註冊設定] 區段上，執行下列步驟，然後按一下 [儲存]：
   
   ![系統管理](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")
   
   1. 在 [啟用新的使用者] 選取 [不需要啟用]。
   2. 在 [使用者資料更新] 選取 [Veracode 使用者資料喜好設定]。
   3. 針對 [SAML 屬性詳細資料] ，請選取下列選項：
      * **[使用者角色]**
      * **[原則系統管理員]**
      * **[檢閱者]**
      * **[安全性負責人]**
      * **[行政人員]**
      * **[傳送者]**
      * **[建立者]**
      * **[所有掃描類型]**
      * **[小組成員資格]**
      * **[預設小組]**
10. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
    
    ![設定單一登入](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configure Single Sign-On")
11. 在頂端的功能表中，按一下 [屬性] **屬性** to open the **SAML Token 屬性** 對話方塊。
    
    ![屬性](./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributes")
12. 若要加入必要的屬性對應，請執行下列步驟：
    
    ![屬性](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")
    
    | 屬性名稱 | 屬性值 |
    |:--- |:--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | 電子郵件 |User.mail |
    
    1. 針對上表中的每個資料列，按一下 [新增使用者屬性] 。
    2. 在 [屬性名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。
    3. 在 [屬性值]  文字方塊中，選取該資料列所顯示的屬性值。
    4. 按一下 [完成] 。
13. 按一下 [套用變更] 。

## <a name="configuring-user-provisioning"></a>設定使用者佈建
若要讓 Azure AD 使用者可以登入 Veracode，則必須將他們佈建到 Veracode。  
若是 Veracode 的情況 來佈建是自動化的工作。  
沒有您適用的動作項目。

第一次嘗試單一登入時，會視需要自動建立使用者。

> [!NOTE]
> 您可以使用任何其他的 Veracode 使用者帳戶建立工具或Veracode 提供的 API 來佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-veracode-perform-the-following-steps"></a>若要指派使用者給 Veracode，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Veracode] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-veracode-tutorial/IC802915.png "Assign Users")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
   ![是](./media/active-directory-saas-veracode-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。




<!--HONumber=Nov16_HO3-->


