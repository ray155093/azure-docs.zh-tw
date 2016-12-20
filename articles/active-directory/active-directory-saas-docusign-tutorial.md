---
title: "教學課程：Azure Active Directory 與 DocuSign 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 DocuSign 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6d2ab680afdd60732b8cf0a39a31ad6f35a70d09


---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>教學課程：Azure Active Directory 與 DocuSign 整合
本教學課程的目的是要示範 Azure 與 DocuSign 的整合。
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* DocuSign 中的租用戶

本教學課程中說明的案例由下列建置組塊組成：

1. [啟用 DocuSign 的應用程式整合](#enabling-the-application-integration-for-docusign) 
2. [設定單一登入](#configuring-single-sign-on) 
3. [設定帳戶佈建](#configuring-account-provisioning) 
4. [指派使用者](#assigning-users) 
   
![設定單一登入][0]

## <a name="enabling-the-application-integration-for-docusign"></a>啟用 DocuSign 的應用程式整合
本節的目的是要說明如何啟用 DocuSign 的應用程式整合。

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>若要啟用 DocuSign 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
    ![設定單一登入][1]
2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![設定單一登入][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊中，按一下 [從資源庫加入應用程式] 。
   
    ![設定單一登入][4]
6. 在搜尋方塊中，輸入 **DocuSign**。
   
    ![設定單一登入][5]
7. 在結果窗格中，選取 [DocuSign]，然後按一下 [完成] 來新增應用程式。
   
    ![設定單一登入][6]

## <a name="configuring-single-sign-on"></a>設定單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 DocuSign。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [DocuSign] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][7]
2. 在 [要如何讓使用者登入 DocuSign] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入][8]
3. 在 [設定應用程式設定]  頁面上，執行下列步驟：
   
    ![設定單一登入][61]
   
    a. 在 [登入 URL] 文字方塊中，輸入 `https://account.docusign.com/*`。  
   
    b. 在 [識別碼] 文字方塊中，輸入 `https://account.docusign.com/*`。  
   
    c. 按 [下一步] 。 

    > [!TIP] 
    > [登入 URL] 和 [識別碼] 值只是預留位置。 本主題稍後會提供如何擷取環境的實際值的指示。


1. 在 [設定在 DocuSign 單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案。
   
    ![設定單一登入][10]
2. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **DocuSign 系統管理入口網站** 。
3. 在左側導覽功能表中按一下 [網域] 。
   
    ![設定單一登入][51]
4. 在右窗格中，按一下 [宣告網域] 。
   
    ![設定單一登入][52]
5. 在 [宣告網域] 對話方塊，於 [網域名稱] 文字方塊內輸入您的公司網域，然後按一下 [宣告]。 確定您已驗證網域，而且狀態為作用中。
   
    ![設定單一登入][53]
6. 在左側的功能表中，按一下 [識別提供者]   
   
    ![設定單一登入][54]
7. 在右窗格中，按一下 [新增識別提供者] 。 
   
    ![設定單一登入][55]
8. 在 [識別提供者設定]  頁面上執行下列步驟：
   
    ![設定單一登入][56]

    a. 在 [名稱] 文字方塊中，輸入組態的唯一名稱。 請勿使用空格。

    b. 在 Azure 傳統入口網站中，複製 [簽發者 URL]，然後貼到 [識別提供者簽發者] 文字方塊中。

    c. 在 Azure 傳統入口網站中，複製 [遠端登入 URL]，然後貼到 [識別提供者登入 URL] 文字方塊中。

    d. 在 Azure 傳統入口網站中，複製 [遠端登出 URL]，然後貼到 [識別提供者登出 URL] 文字方塊中。

    e. 選取 [登入驗證要求]。

    f. 選取 [POST] 做為 [驗證要求傳送方式]。

    g. 選取 [POST] 做為 [登出要求傳送方式]。 


1. 在 [自訂屬性對應] 區段中，選擇您想要與 Azure AD 宣告對應的欄位。 在此範例中，**emailaddress** 宣告的對應值是 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。 這是 Azure AD 針對電子郵件宣告所提供的預設宣告名稱。 
   
    > [!NOTE]
    > 請使用適當的 **使用者識別碼** ，將使用者從 Azure AD 對應到 DocuSign 使用者對應。 選取適當的欄位，並根據組織的設定輸入適當的值。
    > 
    > 
   
    ![設定單一登入][57]
2. 在 [識別提供者憑證] 區段中按一下 [新增憑證]，然後上傳您已從 Azure AD 傳統入口網站下載的憑證。   
   
    ![設定單一登入][58]
3. 按一下 [儲存] 。
4. 在 [識別提供者] 區段中，按一下 [動作]，然後按一下 [端點]。   
   
    ![設定單一登入][59]
5. 在 Azure 傳統入口網站上，回到 [設定應用程式設定]  頁面。 
6. 在 **DocuSign 系統管理入口網站**的 [檢視 SAML 2.0 端點] 區段中，執行下列步驟：
   
    ![設定單一登入][60]
   
    a. 複製 [服務提供者簽發者 URL]，然後將它貼入 Azure 傳統入口網站上的 [識別碼] 文字方塊。
   
    b. 複製 [服務提供者登入 URL]，然後將它貼入 Azure 傳統入口網站上的 [登入 URL] 文字方塊。
   
    c.  按一下 [關閉]。  
7. 在 Azure 傳統入口網站上按 [下一步] 。 
8. 在 Azure 傳統入口網站中，選取**單一登入設定確認**，然後按 [下一步]。
   
    ![應用程式][14]
9. 在 [單一登入確認] 頁面上，按一下 [完成]。
   
    ![應用程式][15]

## <a name="configuring-account-provisioning"></a>設定帳戶佈建
本節的目的是要說明如何對 DocuSign 啟用 Active Directory 使用者帳戶的使用者佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 在 **Azure 傳統入口網站**的 [DocuSign 應用程式整合] 頁面上，按一下 [設定帳戶佈建] 以開啟 [設定使用者佈建] 對話方塊。
   
    ![設定帳戶佈建][30]
2. 在 [設定及管理員認證] 頁面上，若要啟用自動使用者佈建，請提供具有足夠權限的 DocuSign 帳戶認證，然後按 [下一步]。 
   
    ![設定帳戶佈建][31]
3. 在 [測試連接] 對話方塊中，按一下 [開始測試]，在測試成功時按 [下一步]。
   
    ![設定帳戶佈建][32]
4. 在 [確認] 頁面中按一下 [完成]。
   
    ![設定帳戶佈建][33]

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>若要將使用者指派給 DocuSign，請執行下列步驟：
1. 在 **Azure 傳統入口網站**中建立測試帳戶。
2. 在 [DocuSign 應用程式整合] 頁面上，按一下 [指派使用者]。
   
    ![指派使用者][40]
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
    ![指派使用者][41]

請等候 10 分鐘並確認帳戶已同步至 DocuSign。

在第一個驗證步驟中，您可以在 Azure 傳統入口網站的 DocuSign 應用程式整合頁面上，按一下 D 的儀表板來檢查佈建狀態。

![指派使用者][42]

成功完成的使用者佈建週期會以相關狀態表示：

![指派使用者][43]

如果要測試您的單一登入設定，請開啟存取面板。

如需存取面板的詳細資訊，請參閱〈存取面板簡介〉。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png



<!--HONumber=Nov16_HO3-->


