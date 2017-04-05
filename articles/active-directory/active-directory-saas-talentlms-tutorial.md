---
title: "教學課程：Azure Active Directory 與 TalentLMS 整合 | Microsoft Docs"
description: "了解如何使用 TalentLMS 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: a9de3004a1968f514227f0ba5dfde0f562a2b392
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>教學課程：Azure Active Directory 與 TalentLMS 整合
本教學課程的目的是要示範 Azure 與 TalentLMS 的整合。  

本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* TalentLMS 租用戶

完成本教學課程之後，您指派給 TalentLMS 的 Azure AD 使用者就能夠從您的 TalentLMS 公司網站 (服務提供者起始登入)，或使用 [存取面板](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 TalentLMS 的應用程式整合
2. 設定單一登入 (SSO)
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-talentlms-tutorial/IC777289.png "案例")

## <a name="enable-the-application-integration-for-talentlms"></a>啟用 TalentLMS 的應用程式整合
本節的目的是要說明如何啟用 TalentLMS 的應用程式整合。

**若要啟用 TalentLMS 的應用程式整合，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式](./media/active-directory-saas-talentlms-tutorial/IC700994.png "應用程式")

4. 按一下頁面底部的 [新增]  。
   
    ![新增應用程式](./media/active-directory-saas-talentlms-tutorial/IC749321.png "新增應用程式")

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![從資源庫新增應用程式](./media/active-directory-saas-talentlms-tutorial/IC749322.png "從資源庫新增應用程式")

6. 在**搜尋方塊**中，輸入 **TalentLMS**。
   
    ![應用程式資源庫](./media/active-directory-saas-talentlms-tutorial/IC777290.png "應用程式資源庫")

7. 在結果窗格中，選取 [TalentLMS]，然後按一下 [完成] 以加入應用程式。
   
   ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

## <a name="configure-single-sign-on"></a>設定單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 TalentLMS。

設定 TalentLMS 的 SSO 需要您從憑證擷取指紋值。  

如果您不熟悉這個程序，請參閱 [如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

**若要設定單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [TalentLMS] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-talentlms-tutorial/IC777292.png "設定單一登入")

2. 在 [您希望使用者如何登入 TalentLMS] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-talentlms-tutorial/IC777293.png "設定單一登入")
3. 在 [設定應用程式 URL] 頁面的 [TalentLMS 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<tenant-name\>.TalentLMSapp.com*"，然後按 [下一步]。
   
    ![登入 URL](./media/active-directory-saas-talentlms-tutorial/IC777294.png "登入 URL")

4. 在 [設定在 TalentLMS 單一登入] 頁面上，若要下載您的憑證，請按一下 [下載憑證]，然後將憑證檔案以 **c:\\TalentLMS.cer** 方式儲存在本機。
   
    ![設定單一登入](./media/active-directory-saas-talentlms-tutorial/IC777295.png "設定單一登入")

5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 TalentLMS 公司網站。

6. 在 [帳戶和設定] 區段中，按一下 [使用者] 索引標籤。
   
    ![帳戶和設定](./media/active-directory-saas-talentlms-tutorial/IC777296.png "帳戶和設定")

7. 按一下 [單一登入 (SSO)] 。

8. 在 [單一登入] 區段中，執行下列步驟：
   
    ![單一登入](./media/active-directory-saas-talentlms-tutorial/IC777297.png "單一登入")   
  1. 從 [SSO 整合類型] 清單中，選取 [SAML 2.0]。
  2. 在 Azure 傳統入口網站的 [設定在 TalentLMS 單一登入] 對話方塊頁面上，複製 [識別提供者 ID] 值，然後將它貼到 [識別提供者 (IdP)] 文字方塊中。
  3. 從匯出的憑證複製 [指紋] 值，然後將它貼入 [憑證指紋] 文字方塊。
      
     >[!TIP]
     >如需詳細資訊，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。 
     >    

  4. 在 Azure 傳統入口網站的 [設定在 TalentLMS 單一登入] 對話方塊頁面上，複製 [遠端登入 URL] 值，然後將它貼到 [遠端登入 URL] 文字方塊中。 
  5. 在 Azure 傳統入口網站的 [設定在 TalentLMS 單一登入] 對話方塊頁面上，複製 [遠端登出 URL] 值，然後將它貼到 [遠端登出 URL] 文字方塊中。
  6. 填寫下列各項： 
    * 在 [TargetedID] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
    * 在 [名字] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    * 在 [姓氏] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**。
    * 在 [電子郵件] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
  7. 按一下 [儲存] 。

9. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-talentlms-tutorial/IC777298.png "設定單一登入")

## <a name="configure-user-provisioning"></a>設定使用者佈建
若要讓 Azure AD 使用者可以登入 TalentLMS，則必須將他們佈建到 TalentLMS。  

* TalentLMS 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **TalentLMS** 租用戶。

2. 按一下 [使用者]，然後按一下 [加入使用者]。

3. 在 [加入使用者]  對話頁面上，執行下列步驟：
   
    ![新增使用者](./media/active-directory-saas-talentlms-tutorial/IC777299.png "新增使用者")   
  1. 在下列文字方塊中輸入該 Azure AD 使用者帳戶的相關屬性值：**名字**、**姓氏**、**電子郵件地址**。
  2. 按一下 [加入使用者] 。

>[!NOTE]
>您可以使用任何其他的 TalentLMS 使用者帳戶建立工具或 TalentLMS 提供的 API 來佈建 AAD 使用者帳戶。
>  

## <a name="assign-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

**若要指派使用者給 TalentLMS，請執行下列步驟：**

1. 在 Azure 傳統入口網站中建立測試帳戶。

2. 在 [TalentLMS]**** 應用程式整合頁面上，按一下 [指派使用者]。
   
    ![指派使用者](./media/active-directory-saas-talentlms-tutorial/IC777300.png "指派使用者")

3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
    ![是](./media/active-directory-saas-talentlms-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


