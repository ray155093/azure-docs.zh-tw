---
title: "教學課程：Azure Active Directory 與 Deputy 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Deputy 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: c853d61257493d73cd0f8a51a15f0389e1e83cf4
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>教學課程：Azure Active Directory 與 Deputy 整合
本教學課程旨在說明如何將 Deputy 與 Azure Active Directory (Azure AD) 整合。

Deputy 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制誰有 Deputy 的存取權
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Deputy (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
如要設定 Azure AD 與 Deputy 的整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 Deputy 單一登入 (SSO) 功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Deputy
2. 設定並測試 Azure AD 單一登入

## <a name="adding-deputy-from-the-gallery"></a>從資源庫新增 Deputy
若要設定將 Deputy 整合到 Azure AD 中，您需要從資源庫將 Deputy 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Deputy，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Deputy**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)
7. 在結果窗格中，選取 [Deputy]，然後按一下 [完成] 以新增應用程式。
   
    ![選取資源庫中的應用程式](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Deputy 搭配運作的 Azure AD 單一登入。

如要讓單一登入順利運作，Azure AD 必須要知道與某位 Azure AD 使用者互相對應的 Deputy 使用者。 換句話說，必須要建立某位 Azure AD 使用者與 Deputy 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法，是將 Azure AD 中**使用者名稱**的值，指派為 Deputy 中 **Username** 的值。

如要設定及測試搭配 Deputy 的 Azure AD 單一登入，您需要完成下列構成元素：

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Deputy 測試使用者](#creating-a-deputy-test-user)** - 在 Deputy 中建立 Britta Simon 的對應項目，且該項目必須與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，然後在您的 Deputy 應用程式中設定單一登入。

**若要使用 Deputy 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在傳統入口網站的 **Deputy** 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [您希望使用者如何登入 Deputy] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)
3. 在 [設定應用程式設定] 對話方塊頁面上，如果您想要以 **IDP 起始模式**設定應用程式，請執行下列步驟，然後按 [下一步]：
   
    ![設定單一登入](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)
  1. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://<your-subdomain>.<region>.deputy.com`。
  2. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`。
  3. 按 [下一步] 。
4. 如果您想要在 [設定應用程式設定] 對話方塊頁面上以 **SP 起始模式**設定應用程式，則請按一下 [顯示進階設定 (選擇性)]，然後輸入**登入 URL** 並按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)
   1. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<your-subdomain>.<region>.deputy.com`。
   2. 按 [下一步] 。
   
   > [!NOTE]
   > Deputy 區域尾碼是選擇性的，或者應該使用下列其中一個︰au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an
   > 
   > 
5. 在 [設定在 Deputy 單一登入] 頁面上，執行下列步驟，然後按 [下一步]：
   
    ![設定單一登入](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)
   1. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
6. 瀏覽至下列 URL：https://(your-subdomain).deputy.com/exec/config/system_config。 移至 [安全性設定]，然後按一下 [編輯]。
   
    ![設定單一登入](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)
7. 在 Azure 傳統入口網站的 [設定在 Deputy 單一登入] 頁面上， 複製 [SAML SSO URL]。 
8. 在此 [安全性設定]  頁面上，執行下列步驟。
   
![設定單一登入](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

   1. 啟用**社交登入**。
   2. 在記事本中開啟您的 Base64 編碼的憑證，將其內容複製到剪貼簿，然後貼到 [OpenSSL 憑證] 文字方塊。
   3. 在 [SAM SSO URL] 文字方塊中，輸入︰`https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
   4. 在 [SAM SSO URL] 文字方塊中，以您的子網域取代 `<your subdomain>`。
   5. 在 [SAM SSO URL] 文字方塊中，以您從 Azure 傳統入口網站複製的 SAML SSO URL 取代 `<saml sso url>`。
   6. 按一下 [儲存設定] 。
9. 在傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
10. 在 [單一登入確認] 頁面上，按一下 [完成]。  
    
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

   1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)
   
   1. 在 [名字] 文字方塊中，輸入 **Britta**。  
   2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   4. 在 [角色] 清單中選取 [使用者]。
   5. 按 [下一步] 。
   
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)
   
   1. 記下 [新密碼] 的值。
   2. 按一下 [完成]。   

### <a name="creating-a-deputy-test-user"></a>建立 Deputy 測試使用者
若要讓 Azure AD 使用者可以登入 Deputy，則必須將他們佈建到 Deputy。 Deputy 需以手動的方式佈建。

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要佈建使用者帳戶，請執行下列步驟：
1. 以系統管理員身分登入您的 Deputy 公司網站。
2. 在導覽窗格的頂端，按一下 [人員] 。
   
   ![人員](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "人員")
3. 按一下 [新增人員] 按鈕，然後按一下 [新增一個人]。
   
   ![新增人員](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "新增人員")
4. 執行下列步驟，然後按一下 [儲存並邀請]。
   
   ![新增使用者](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "新增使用者")
   
  1. 在 [名稱] 文字方塊中，輸入 **Britta** 和 **Simon**。  
  2. 在 [電子郵件] 文字方塊中，輸入您要佈建的 Azure AD 帳戶的電子郵件地址。
  3. 在 [公司] 文字方塊中，輸入企業名稱。
  4. 按一下 [儲存並邀請] 按鈕。
   
   > [!NOTE]
   > AAD 帳戶的持有者會收到一封電子郵件，並遵循連結在啟用其帳戶前進行確認。 您可以使用任何其他的 Deputy 使用者帳戶建立工具或 Deputy 提供的 API 來佈建 AAD 使用者帳戶。
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標，是要授與 Britta Simon 對 Deputy 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200]

**若要將 Britta Simon 指派給 Deputy，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201]
2. 在應用程式清單中，選取 [Deputy] 。
   
    ![設定單一登入](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 Deputy 圖格時，應該會自動登入 Deputy 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png

