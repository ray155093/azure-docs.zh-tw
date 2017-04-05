---
title: "教學課程：Azure Active Directory 與 ThousandEyes 整合 | Microsoft Docs"
description: "了解如何使用 ThousandEyes 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 7bc96e6a711c70f9c5fa5daa4e059d9d7c04a134
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>教學課程：Azure Active Directory 與 ThousandEyes 整合
本教學課程的目的是示範如何設定 Azure Active Directory (Azure AD) 與 ThousandEyes 之間的單一登入。

本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* 已啟用 ThousandEyes 單一登入 (SSO) 的訂用帳戶

完成本教學課程之後，您已指派 ThousandEyes 存取權的 AAD 使用者就能夠單一登入您 ThousandEyes 公司網站上的應用程式 (服務提供者起始的登入)，或是使用 AAD 存取面板進行單一登入。

1. 啟用 ThousandEyes 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "案例")

## <a name="enable-the-application-integration-for-thousandeyes"></a>啟用 ThousandEyes 的應用程式整合
本節的目的是要說明如何啟用 ThousandEyes 的應用程式整合。

**若要啟用 ThousandEyes 的應用程式整合，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
    ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "應用程式")

4. 按一下頁面底部的 [新增]  。
   
    ![新增應用程式](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "新增應用程式")

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![從資源庫新增應用程式](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "從資源庫新增應用程式")

6. 在**搜尋方塊**中，輸入 **ThousandEyes**。
   
    ![應用程式資源庫](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "應用程式資源庫")
7. 在結果窗格中，選取 [ThousandEyes]，然後按一下 [完成] 以新增應用程式。
   
    ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

## <a name="configure-single-sign-on"></a>設定單一登入
本節說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure Active Directory 帳戶驗證至 ThousandEyes。

**若要設定單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [ThousandEyes] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "設定單一登入")

2. 在 [您希望使用者如何登入 ThousandEyes] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "設定單一登入")

3. 在 [設定應用程式 URL] 頁面的 [ThousandEyes 登入 URL] 文字方塊中，輸入使用者用來登入 ThousandEyes 應用程式的 URL (例如："*https://app.thousandeyes.com/login/sso*")，然後按 [下一步]。 
   
    ![設定應用程式 URL](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "設定應用程式 URL")

4. 在 [設定在 ThousandEyes 單一登入] 頁面上，若要下載您的憑證，請按一下 [下載憑證]，然後將憑證檔案儲存在本機電腦上。
   
    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "設定單一登入")

5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **ThousandEyes** 公司網站。

6. 在頂端的功能表中，按一下 [設定] 。
   
    ![設定](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "設定")

7. 按一下 [帳戶] 
   
    ![帳戶](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "帳戶")

8. 按一下 [安全性與驗證] 索引標籤。
   
    ![安全性和驗證](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "安全性和驗證")

9. 在 [設定單一登入]  區段中，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "設定單一登入")
  1. 選取 [啟用單一登入] 。
  2. 在 Microsoft Azure 傳統入口網站的 [設定在 ThousandEyes 單一登入] 頁面上，複製 [遠端登入 URL] 值，然後將它貼到 [登入頁面 URL] 文字方塊中。
  3. 在 Microsoft Azure 傳統入口網站的 [設定在 ThousandEyes 單一登入] 頁面上，複製 [遠端登出 URL] 值，然後將它貼到 [登出頁面 URL] 文字方塊中。
  4. 在 Microsoft Azure 傳統入口網站的 [設定在 ThousandEyes 單一登入] 頁面上，複製 [簽發者 URL] 值，然後將它貼到 [識別提供者簽發者] 文字方塊中。
  5. 在 [識別提供者憑證] 中，按一下 [選擇檔案]，然後上傳您已從 Microsoft Azure 傳統入口網站下載的憑證。
  6. 按一下 [儲存] 。

10. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
    
    ![設定單一登入](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "設定單一登入")

## <a name="configure-user-provisioning"></a>設定使用者佈建
若要讓 Azure AD 使用者可以登入 ThousandEyes，則必須將他們佈建到 ThousandEyes。  
ThousandEyes 需以手動的方式佈建。

**若要將使用者帳戶佈建到 ThousandEyes，請執行下列步驟：**

1. 以系統管理員身分登入您的 ThousandEyes 公司網站。

2. 按一下 [設定] 。
   
    ![設定](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "設定")

3. 按一下 [帳戶] 。
   
    ![帳戶](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "帳戶")

4. 按一下 [帳戶和使用者] 索引標籤。
   
    ![帳戶和使用者](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "帳戶和使用者")

5. 在 [加入使用者和帳戶] 區段中，執行下列步驟：
   
    ![新增使用者帳戶](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "新增使用者帳戶")   
  1. 在相關的文字方塊中，輸入您要佈建之有效 Azure Active Directory 帳戶的 [名稱]、[電子郵件] 及其他詳細資料。
  2. 按一下 [加入使用者至帳戶] 按鈕。
      
     >[!NOTE]
     >AAD 帳戶的持有者會收到一封包含連結的電子郵件，以進行確認並啟用帳戶。
     >  

>[!NOTE]
>您可以使用任何其他的 ThousandEyes 使用者帳戶建立工具或 ThousandEyes 提供的 API 來佈建 AAD 使用者帳戶。
>  

## <a name="assign-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

**若要指派使用者給 ThousandEyes，請執行下列步驟：**

1. 在 Azure 傳統入口網站中建立測試帳戶。

2. 在 [ThousandEyes] 應用程式整合頁面上，按一下 [指派使用者]。
   
    ![指派使用者](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "指派使用者")

3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
    ![是](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


