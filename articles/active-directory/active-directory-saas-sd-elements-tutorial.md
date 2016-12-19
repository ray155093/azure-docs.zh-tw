---
title: "教學課程：Azure Active Directory 與 SD Elements 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 SD Elements 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 53aa0a84a7f22c8cda5144eb6e1b82f38b72acb8


---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>教學課程：Azure Active Directory 與 SD Elements 整合
本教學課程旨在說明如何整合 SD Elements 與 Azure Active Directory (Azure AD)。  
SD Elements 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 SD Elements 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SD Elements (單一登入)
* 您可以在 Azure Active Directory 集中管理您的帳戶 

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 SD Elements 的整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 SD Elements 單一登入的訂用帳戶

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

1. 從資源庫新增 SD Elements
2. 設定並測試 Azure AD 單一登入

## <a name="adding-sd-elements-from-the-gallery"></a>從資源庫新增 SD Elements
若要設定 SD Elements 與 Azure AD 的整合，您需要從資源庫將 SD Elements 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 SD Elements，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]

6. 在搜尋方塊中，輸入 **SD Elements**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)

7. 在結果窗格中，選取 [SD Elements]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 SD Elements 來設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 SD Elements 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 SD Elements 中的相關使用者之間建立連結關聯性。  
建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指派為 SD Elements 中 **Username** 的值。

若要使用 SD Elements 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 SD Elements 測試使用者](#creating-a-sd-elements-test-user)** - 使 SD Elements 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節目標是能在 Azure 傳統入口網站啟用 Azure AD 單一登入，並在您的 SD Elements 應用程式中設定單一登入。

SD Elements 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 **SAML 權杖屬性** 組態中。 以下螢幕擷取畫面顯示上述的範例︰

![設定單一登入](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png) 

**若要使用 SD Elements 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [SD Elements] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 

2. 在 [要如何讓使用者登入 SD Elements] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png) 

3. 在 [設定應用程式設定]  對話方塊頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png) 

    a. 在 [簽發者] 文字方塊中，以下列形式輸入您租用戶的簽發者 URL：*https://\<您的租用戶名稱\>.sdelements.com/sso/saml2/metadata*

    b. 在 [回覆 URL] 文字方塊中，以下列形式輸入您租用戶的回覆 URL：*https://\<您的租用戶名稱\>.sdelements.com/sso/saml2/acs/*       

    > [!NOTE] 
    > 如果您的租用戶需要實際的簽發者 URL 和回覆 URL，請連絡您的 [SD Elements 支援小組](mailto:support@sdelements.com)。

    c. 按 [下一步] 。


1. 在 [設定在 SD Elements 單一登入]  頁面上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png) 
   
    a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。
   
    b. 按 [下一步] 。
2. 若要啟用單一登入，請連絡您 [SD Elements 支援小組](mailto:support@sdelements.com) ，並提供下載的憑證檔案。
3. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 SD Elements 租用戶。
4. 在頂端的功能表中按一下 [系統]，然後按一下 [單一登入]。 
   
    ![設定單一登入](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) 
5. 在 [單一登入設定]  對話方塊上，執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. [SSO 類型] 請選取 [SAML]。
   
    b.這是另一個 C# 主控台應用程式。 在 Azure 傳統入口網站中的 [設定在 SD Elements 單一登入] 對話頁面上，複製 [簽發者 URL] 值，然後將它貼至 [身分識別提供者實體識別碼] 文字方塊中。
   
    c. 在 Azure 傳統入口網站中的 [設定在 SD Elements 單一登入] 對話頁面上，複製 [單一登入服務 URL] 值，然後將它貼至 [身分識別提供者單一登入服務] 文字方塊中。
   
    d. 按一下 [儲存] 。
6. 在 Azure 傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
   
    ![Azure AD 單一登入][10]
7. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![Azure AD 單一登入][11]
8. 在頂端的功能表中，按一下 [屬性] **屬性** to open the **SAML Token 屬性** 對話方塊。 
   
    ![設定單一登入][21]
9. 針對下表中的每個資料列，執行下列步驟：
   
   | 屬性名稱 | 屬性值 |
   | --- | --- |
   | 電子郵件 |user.mail |
   | firstname |user.givenname |
   | lastname |user.surname |

    a. 按一下 [加入使用者屬性] 。 

    ![設定單一登入][23]

    b. 在 [屬性名稱] 文字方塊中，輸入 [屬性名稱] 和 [屬性值]，然後選取為該資料列顯示的 [屬性值]。

    ![設定單一登入][22]

    c. 按一下 [加入使用者屬性] 。 

    ![設定單一登入][23]

1. 按一下 [套用變更] 。 
   
    ![設定單一登入][24]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 傳統入口網站中建立名為 Britta Simon 的測試使用者。  

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png) 
   
   a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
   b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-a-sd-elements-test-user"></a>建立 SD Elements 測試使用者
本節目標是在 SD Elements 中建立名為 Britta Simon 的使用者。 在 SD Elements 的案例中，以手動工作建立 SD Elements 使用者。

**若要在 SD Elements 中建立 Britta Simon，請執行下列步驟：**

1. 在 Wed 瀏覽器視窗中，以系統管理員身分登入您的 SD Elements 公司網站。
2. 在頂端的功能表中，按一下 [使用者管理]，然後按一下 [使用者]。
   
   ![建立 SD Elements 測試使用者](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) 
3. 按一下 [新增使用者]。
   
   ![建立 SD Elements 測試使用者](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png) 
4. 在 [新增使用者] 對話方塊上，執行下列步驟：
   
   ![建立 SD Elements 測試使用者](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
   a. 在 [電子郵件] 文字方塊中，輸入 Britta 在 Azure AD 中的電子郵件地址。
   
   b. 在 [名字] 文字方塊中，輸入 **Britta**。
   
   c. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   d. 針對 [角色]，選取 [使用者]。 
   
   e. 按一下 [建立使用者] 。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節目標是授與 Britta Simon 對 SD Elements 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 SD Elements，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [SD Elements] 。
   
    ![設定單一登入](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。  
當您在存取面板中按一下 [SD Elements] 圖格時，應該會自動登入您的 SD Elements 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


