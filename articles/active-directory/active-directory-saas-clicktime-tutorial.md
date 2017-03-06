---
title: "教學課程：Azure Active Directory 與 ClickTime 整合 | Microsoft Docs"
description: "了解如何使用 ClickTime 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: d5171cdc9048837385bfb99d553e496a9f56846e
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>教學課程：Azure Active Directory 與 ClickTime 整合
在本教學課程中，您會了解如何整合 ClickTime 與 Azure Active Directory (Azure AD)。

ClickTime 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 ClickTime 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 ClickTime (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 ClickTime 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 ClickTime 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 ClickTime
2. 設定並測試 Azure AD 單一登入

## <a name="adding-clicktime-from-the-gallery"></a>從資源庫新增 ClickTime
本節的目的是要說明如何啟用 ClickTime 的應用程式整合。

### <a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>若要啟用 ClickTime 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-clicktime-tutorial/tic700994.png "應用程式")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-clicktime-tutorial/tic749321.png "新增應用程式")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從資源庫新增應用程式](./media/active-directory-saas-clicktime-tutorial/tic749322.png "從資源庫新增應用程式")
6. 在**搜尋方塊**中，輸入 **ClickTime**。
   
   ![應用程式資源庫](./media/active-directory-saas-clicktime-tutorial/tic777275.png "應用程式資源庫")
7. 在結果窗格中，選取 [ClickTime]，然後按一下 [完成] 以加入應用程式。
   
   ![ClickTime](./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 ClickTime 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 ClickTime 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 ClickTime 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 ClickTime 中 **Username** 的值。

若要設定及測試與 ClickTime 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 ClickTime 測試使用者](#creating-a-clicktime-test-user)** - 在 ClickTime 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 ClickTime。  

> [!IMPORTANT]
> 為了能夠設定對您的 ClickTime 租用戶進行單一登入，您必須先連絡 ClickTime 技術支援人員以啟用這項功能。
> 
> 

**若要設定與 ClickTime 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [ClickTime] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![啟用單一登入](./media/active-directory-saas-clicktime-tutorial/tic777277.png "啟用單一登入")
2. 在 [要如何讓使用者登入 ClickTime] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
   ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tic777278.png "設定單一登入")
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 
   
    a. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：**https://app.clicktime.com/sp/**
   
    b.這是另一個 C# 主控台應用程式。 在 [識別碼] 文字方塊中，以下列模式輸入 URL：**https://app.clicktime.com/Login/**
   
    c. 按一下 [下一步] 
4. 於 [在 ClickTime 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。
   
   ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tic777279.png "設定單一登入")
5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 ClickTime 公司網站。
6. 在頂端工具列中，按一下 [喜好設定]，然後按一下 [安全性設定]。
7. 在 [單一登入喜好設定]  組態區段中，執行下列步驟：
   
   ![安全性設定](./media/active-directory-saas-clicktime-tutorial/tic777280.png "安全性設定")
   
   a.  選取 [允許]，以搭配 **Azure AD** 使用單一登入 (SSO) 進行登入。
   
   b.  在 Azure 傳統入口網站的 [在 ClickTime 設定單一登入] 對話頁面上，複製 [單一登入服務 URL] 值，然後將它貼至 [識別提供者端點] 文字方塊中。
   
   c.  在**記事本**中開啟 base-64 編碼的憑證，複製其內容，然後貼到 [X.509 憑證] 文字方塊中。
   
   d.  按一下 [儲存] 。
8. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tic777281.png "設定單一登入")

## <a name="configuring-user-provisioning"></a>設定使用者佈建
若要讓 Azure AD 使用者可以登入 ClickTime，必須將他們佈建到 ClickTime。  
ClickTime 需以手動方式佈建。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要佈建使用者帳戶，請執行下列步驟：
1. 登入您的 **ClickTime** 租用戶。
2. 在頂端工具列中，按一下 [公司]，然後按一下 [人員]。
   
   ![人員](./media/active-directory-saas-clicktime-tutorial/tic777282.png "人員")
3. 按一下 [新增人員] 。
   
   ![新增人員](./media/active-directory-saas-clicktime-tutorial/tic777283.png "新增人員")
4. 在 [新人員] 區段中，執行下列步驟：
   
   ![人員](./media/active-directory-saas-clicktime-tutorial/tic777284.png "人員")
   
   a.  在 [電子郵件地址] 文字方塊中，輸入您的 Azure AD 帳戶的電子郵件地址。
   
   b.  在 [全名] 文字方塊中，輸入您 Azure AD 帳戶的名稱。  
   
   > [!NOTE]
   > 您可以視需要設定新人員物件的其他屬性。
   > 
   > 
   
   c.  按一下 [儲存] 。

> [!NOTE]
> 您可以使用任何其他的 ClickTime 使用者帳戶建立工具或 ClickTime 提供的 API 來佈建 Azure AD 使用者帳戶。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會把 ClickTime 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200]

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

**若要將 Britta Simon 指派給 ClickTime，請執行下列步驟**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [ClickTime] 。
   
    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

## <a name="testing-single-sign-on"></a>測試單一登入
在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [ClickTime] 圖格時，應該會自動登入您的 ClickTime 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png

