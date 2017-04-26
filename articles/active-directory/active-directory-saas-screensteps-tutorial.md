---
title: "教學課程：Azure Active Directory 與 ScreenSteps 整合 | Microsoft Docs"
description: "了解如何使用 ScreenSteps 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7fbb8874367bda9be618332947ffa2c9942fe4ec
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>教學課程：Azure Active Directory 與 ScreenSteps 整合
本教學課程的目的是要示範 Azure 與 ScreenSteps 的整合。
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* ScreenSteps 租用戶

完成本教學課程之後，您指派給 ScreenSteps 的 Azure AD 使用者就能在 ScreenSteps 公司網站 (服務提供者起始登入) 使用單一登入 (SSO) 來登入應用程式，或使用[存取面板](active-directory-saas-access-panel-introduction.md)來登入。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 ScreenSteps 的應用程式整合
2. 設定單一登入 (SSO) 
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-screensteps-tutorial/IC778516.png "案例")

## <a name="enable-the-application-integration-for-screensteps"></a>啟用 ScreenSteps 的應用程式整合
本節的目的是要說明如何啟用 ScreenSteps 的應用程式整合。

**若要啟用 ScreenSteps 的應用程式整合，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。

    ![應用程式](./media/active-directory-saas-screensteps-tutorial/IC700994.png "應用程式")
4. 按一下頁面底部的 [新增]  。

    ![新增應用程式](./media/active-directory-saas-screensteps-tutorial/IC749321.png "新增應用程式")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。

    ![從資源庫新增應用程式](./media/active-directory-saas-screensteps-tutorial/IC749322.png "從資源庫新增應用程式")
6. 在**搜尋方塊**中，輸入 **ScreenSteps**。

    ![應用程式資源庫](./media/active-directory-saas-screensteps-tutorial/IC778517.png "應用程式資源庫")
7. 在結果窗格中，選取 [ScreenSteps]，然後按一下 [完成] 來新增應用程式。

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configure-single-sign-on"></a>設定單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 ScreenSteps 中進行驗證。

**若要設定單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [ScreenSteps] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778519.png "設定單一登入")
2. 在 [要如何讓使用者登入 ScreenSteps] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778520.png "設定單一登入")

3. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 ScreenSteps 公司網站。

4. 按一下 [帳戶管理] 。

    ![帳戶管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "帳戶管理")

5. 按一下 [單一登入] 。

    ![遠端驗證](./media/active-directory-saas-screensteps-tutorial/IC778524.png "遠端驗證")

6. 按一下 [建立單一登入端點]。

    ![遠端驗證](./media/active-directory-saas-screensteps-tutorial/IC778525.png "遠端驗證")

7. 在 [建立單一登入端點] 區段中，執行下列步驟：

    ![建立驗證端點](./media/active-directory-saas-screensteps-tutorial/IC778526.png "建立驗證端點")

    1. 在 [標題]  文字方塊中輸入標題。
    2. 從 [模式] 清單中選取 [SAML]。
    3. 按一下 [建立] 。

8. 編輯新的端點。

    ![編輯端點](./media/active-directory-saas-screensteps-tutorial/IC778528.png "編輯端點")

9. 在 [編輯單一登入端點] 區段中，執行下列步驟：

    ![遠端驗證端點](./media/active-directory-saas-screensteps-tutorial/IC778527.png "遠端驗證端點")

    1. 將 **SAML 取用者 URL** 複製到剪貼簿。

10. 在 Azure 傳統入口網站的 [設定應用程式 URL] 頁面上，於 [ScreenSteps 登入 URL] 文字方塊中貼上 **SAML 取用者 URL**，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-screensteps-tutorial/IC778521.png "設定應用程式 URL")

11. 在 [設定在 ScreenSteps 單一登入] 頁面上，按一下 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778522.png "設定單一登入")


12. 返回 [編輯單一登入端點] 區段，然後執行下列步驟：

    ![遠端驗證端點](./media/active-directory-saas-screensteps-tutorial/IC778527.png "遠端驗證端點")

    1. 按一下 [上傳新的 SAML 憑證檔]，然後上傳所下載的憑證。
    2. 在 Azure 傳統入口網站的 [設定在 ScreenSteps 單一登入] 頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [遠端登入 URL] 文字方塊中。
    3. 在 Azure 傳統入口網站的 [設定在 ScreenSteps 單一登入] 頁面上，複製 [遠端登出 URL] 值，然後將它貼至 [登出 URL] 文字方塊中。
    4. 選取要在佈建使用者時將使用者指派給哪個 [群組]。
    5. 按一下 [更新] 。
    6. 返回 [編輯單一登入端點]。
    7. 按一下 [設為帳戶的預設值] 按鈕，讓所有登入 ScreenSteps 的使用者使用此端點。 或者，您可以按一下 [新增至網站] 按鈕，以對 **ScreenSteps** 中的特定站台使用此端點。


12. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778542.png "設定單一登入")

## <a name="configuring-user-provisioning"></a>設定使用者佈建



## <a name="assign-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

**若要將使用者指派給 ScreenSteps，請執行下列步驟：**

1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [ScreenSteps] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-screensteps-tutorial/IC778548.png "指派使用者")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。


如果要測試您的 SSO 設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


