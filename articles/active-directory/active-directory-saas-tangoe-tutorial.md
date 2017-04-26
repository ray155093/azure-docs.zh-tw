---
title: "教學課程：Azure Active Directory 與 Tangoe 命令高階行動裝置整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Tangoe 命令高階行動裝置之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8ab2dd16940f36411cf9a9f244bd3ec6eaf9749b
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>教學課程：Azure Active Directory 整合 Tangoe 命令高階行動裝置

在本教學課程中，您會了解如何整合 Tangoe 命令高階行動裝置與 Azure Active Directory (Azure AD)。

Tangoe 命令高階行動裝置與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Tangoe 命令高階行動裝置的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Tangoe 命令高階行動裝置單一登入 (SSO)
* 您可以使用 Azure 傳統入口網站集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定與 Tangoe 命令高階行動裝置的 Azure AD 整合，您需要下列項目：

* Azure 訂用帳戶
* Tangoe 命令高階行動裝置單一登入 (SSO) 啟用的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Tangoe 命令高階行動裝置
2. 設定並測試 Azure AD SSO

## <a name="adding-tangoe-command-premium-mobile-from-the-gallery"></a>從資源庫新增 Tangoe 命令高階行動裝置
若要設定 Tangoe 命令高階行動裝置與 Azure AD 整合，您需要從資源庫將 Tangoe 命令高階行動裝置新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Tangoe 命令高階行動裝置，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在 [搜尋] 方塊中，輸入 **Tangoe 命令高階行動裝置**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_01.png)
7. 在結果窗格中，選取 [Tangoe 命令高階行動裝置]，然後按一下 [完成] 以新增應用程式。

![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，使用 Tangoe 命令高階行動裝置設定及測試 Azure AD SSO。

若要讓 SSO 運作，Azure AD 必須知道 Tangoe 命令高階行動裝置中與 Azure AD 互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Tangoe 命令高階行動裝置中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指派為 Tangoe 命令高階行動裝置中 **Username** 的值。

若要設定及測試對 Tangoe 命令高階行動裝置的 Azure AD SSO，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Tangoe 命令高階行動裝置測試使用者](#creating-an-tangoe-test-user)** - 在 Tangoe 命令高階行動裝置中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD SSO，並在您的 Tangoe 命令高階行動裝置應用程式中設定 SSO。

**若要設定透過 Tangoe 命令高階行動裝置使用 Azure AD SSO，請執行下列步驟：**

1. 在傳統入口網站的 [Tangoe 命令高階行動裝置] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6]
2. 在 [您希望使用者如何登入 Tangoe 命令高階行動裝置] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_03.png) 
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_04.png) 

   1. 在 [登入 URL] 文字方塊中，輸入使用者使用以下模式登入 Tangoe 命令高階行動裝置應用程式所使用的 URL：**“https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=\<租用戶發行者\>&Target=\<目標頁面 URL\>”**。

   2. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：**"https://sso.tangoe.com/sp/ACS.saml2"**

     >[!NOTE]  
     >如果您不知道 URL 的正確值，您可以使用上述的值做為預留位置，並向 Tangoe 客戶支援部門要求正確的值。
     >

4. 在 [在 Tangoe 命令高階行動裝置設定單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_05.png) 
  1. 按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。
  2. 按 [下一步] 。

5. 若要為應用程式設定 SSO，請連絡 Tangoe 客戶支援部門，並提供下列資訊：

   - 下載的中繼資料檔案
   - **簽發者 URL**
   - **SAML SSO URL**
   - **單一登出服務 URL**

6. 在傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
7. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

* 在 [使用者] 清單中，選取 [Britta Simon] 。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png)
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/create_aaduser_05.png) 
  1. 針對 [使用者類型]，選取 [您組織中的新使用者]。 
  2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
  3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/create_aaduser_06.png) 
   
  1. 在 [名字] 文字方塊中，輸入 **Britta**。  
  2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
  3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
  4. 在 [角色] 清單中選取 [使用者]。
  5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tangoe-tutorial/create_aaduser_08.png) 
   
  1. 記下 [新密碼] 的值。
  2. 按一下頁面底部的 [新增] 。   

### <a name="create-an-tangoe-command-premium-mobile-test-user"></a>建立 Tangoe 命令高階行動裝置測試使用者
在本節中，您要在 Tangoe 命令高階行動裝置中建立名為 Britta Simon 的使用者。 

Tangoe 命令高階行動裝置應用程式需要在應用程式中佈建所有使用者才能執行單一登入。 因此，請利用 Tangoe 客戶支援部門將所有使用者佈建到應用程式。 

>[!NOTE]
>如果您需要手動建立使用者或使用者批次，您必須連絡 Tangoe 命令高階行動裝置支援小組。
>
>

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會把 Tangoe 命令高階行動裝置的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要指派 Brita Simon 給 Tangoe 命令高階行動裝置，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。

 ![指派使用者][201] 

2. 在應用程式清單中，選取 [Tangoe 命令高階行動裝置] 。

 ![設定單一登入](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_50.png) 
 
3. 在頂端的功能表中，按一下 [使用者] 。

 ![指派使用者][203] 

4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。

![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
在本節中，您會使用存取面板來測試您的 Azure AD SSO 組態。

當您按一下存取面版中的 [Tangoe 命令高階行動裝置] 圖格，您應該會自動登入 Tangoe 命令高階行動裝置應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_205.png

