---
title: "教學課程：Azure Active Directory 與 Intacct 整合 | Microsoft Docs"
description: "了解如何使用 Intacct 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 20db13c5ba67fcc402db5c692842719dd9a34eb1
ms.openlocfilehash: 0ea77d5f9c5b139ce6f87bc0cdd817166b03c22e
ms.lasthandoff: 03/01/2017

---

# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>教學課程：Azure Active Directory 與 Intacct 整合
本教學課程的目的是要示範 Azure 與 Intacct 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* Intacct 租用戶

完成本教學課程之後，您指派給 Intacct 的 Azure Active Directory (Azure AD) 使用者就能夠登入您 Intacct 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

* 啟用 Intacct 的應用程式整合
* 設定單一登入
* 設定使用者佈建
* 指派使用者

![案例](./media/active-directory-saas-intacct-tutorial/IC790030.png "案例")

## <a name="enable-the-application-integration-for-intacct"></a>啟用 Intacct 的應用程式整合
本節的目的是要說明如何啟用 Intacct 的應用程式整合。

**若要啟用 Intacct 的應用程式整合，請執行下列步驟：**

1. 在 Azure 傳統入口網站的左窗格中，按一下 [Active Directory] 圖示。

   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

   ![應用程式](./media/active-directory-saas-intacct-tutorial/IC700994.png "應用程式")
4. 按一下頁面底部的 [新增]  。

   ![新增應用程式](./media/active-directory-saas-intacct-tutorial/IC749321.png "新增應用程式")
5. 在 [欲執行動作] 頁面上，按一下 [從資源庫中新增應用程式]。

   ![從資源庫新增應用程式](./media/active-directory-saas-intacct-tutorial/IC749322.png "從資源庫新增應用程式")
6. 在搜尋方塊中，輸入 **Intacct**。

   ![應用程式資源庫](./media/active-directory-saas-intacct-tutorial/IC790031.png "應用程式資源庫")
7. 在結果窗格中，選取 [Intacct]，然後按一下 [完成] 以加入應用程式。

   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")

## <a name="configure-single-sign-on"></a>設定單一登入

本節的目的是要說明如何讓使用者用自己的 Azure AD 帳戶在 Intacct 中進行驗證。 此驗證會使用以 SAML 通訊協定為基礎的同盟。  

在此程序中，您必須建立 Base-64 編碼的憑證檔案。 如果您不熟悉此程序，請參閱 [如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

**若要設定單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [Intacct] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 頁面。

   ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790033.png "設定單一登入")
2. 在 [您希望使用者如何登入 Intacct] 頁面上，選取 [Windows Azure AD 單一登入]，然後按一下 [下一步]。

   ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790034.png "設定單一登入")
3. 在 [設定應用程式 URL] 頁面的 [登入 URL] 方塊中，輸入使用 *https://Intacct.com/company* 模式的 URL，然後按 [下一步]。

   ![設定應用程式 URL](./media/active-directory-saas-intacct-tutorial/IC790035.png "設定應用程式 URL")
4. 在 [設定在 Intacct 單一登入] 頁面上，按一下 [下載憑證]，然後將憑證檔案儲存在您的電腦中。

   ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790036.png "設定單一登入")
5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Intacct 公司網站。
6. 按一下 [公司] 索引標籤，然後按一下 [公司資訊]。

   ![公司](./media/active-directory-saas-intacct-tutorial/IC790037.png "公司")
7. 按一下 [安全性] 索引標籤，然後按一下 [編輯]。

   ![安全性](./media/active-directory-saas-intacct-tutorial/IC790038.png "安全性")
8. 在 [單一登入 (SSO)]  區段中，執行下列步驟：

   ![單一登入](./media/active-directory-saas-intacct-tutorial/IC790039.png "單一登入")

   1. 選取 [啟用單一登入] 。
   2. 在 [身分識別提供者類型]，選取 **SAML 2.0**。
   3. 在 Azure 傳統入口網站中的 [設定在 Intacct 單一登入] 頁面上，複製 [簽發者 URL] 值，然後將它貼至 [簽發者] 方塊中。
   4. 在 Azure 傳統入口網站中的 [設定在 Intacct 單一登入] 頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [登入 URL] 方塊中。
   5. 從您下載的憑證建立「Base-64 編碼」  檔案。 如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。      
   6. 在記事本中開啟您的 Base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [憑證] 方塊中。
   7. 按一下 [儲存] 。
9. 在 Azure 傳統入口網站中，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 頁面。

   ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790040.png "設定單一登入")

## <a name="configure-user-provisioning"></a>設定使用者佈建

若要設定 Azure AD 使用者以便讓他們能夠登入 Intacct，則必須將他們佈建到 Intacct。 在 Intacct 中，佈建是手動工作。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **Intacct** 租用戶。
2. 按一下 [公司] 索引標籤，然後按一下 [使用者]。

   ![使用者](./media/active-directory-saas-intacct-tutorial/IC790041.png "使用者")
3. 按一下 [新增] 索引標籤。

   ![新增](./media/active-directory-saas-intacct-tutorial/IC790042.png "新增")
4. 在 [使用者資訊]  區段中，執行下列步驟：

   ![使用者資訊](./media/active-directory-saas-intacct-tutorial/IC790043.png "使用者資訊")

   1. 在 [使用者資訊] 區段中，為您要佈建的 Azure AD 帳戶輸入 [使用者識別碼]、[姓氏]、[名字]、[電子郵件地址]、[職稱] 和 [電話]。
   2. 選取您要佈建之 Azure AD 帳戶的 [系統管理員權限]。
   3. 按一下 [儲存] 。 Azure AD 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

>[!NOTE]
>若要佈建 Azure AD 使用者帳戶，您可以使用 Intacct 所提供的其他 Intacct 使用者帳戶建立工具或 API。
>

## <a name="assign-users"></a>指派使用者
若要測試組態，您必須將 Azure AD 使用者指派給 Intacct。 使用者在獲得指派後就可以存取應用程式。

**若要指派使用者給 Intacct，請執行下列步驟：**

1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Intacct] 應用程式整合頁面上，按一下 [指派使用者]。

   ![指派使用者](./media/active-directory-saas-intacct-tutorial/IC790044.png "指派使用者")
3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

   ![是](./media/active-directory-saas-intacct-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。


### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Intacct 的存取權授與 Britta Simon，以便設定讓她使用 Azure 單一登入。

![指派使用者][200]

**若要將 Britta Simon 指派給 Intacct，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，移至目錄檢視，移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201]

2. 在應用程式清單中，選取 [Intacct]。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png)

3. 在 [管理] 功能表上，按一下 [使用者和群組]。

    ![指派使用者][202]

4. 按一下 [新增] 按鈕，然後在 [新增指派] 中，選取 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 中，從使用者清單內選取 [Britta Simon]。

6. 在 [使用者和群組] 中，按一下 [選取] 按鈕。

7. 在 [新增指派] 中，按一下 [指派] 按鈕。



### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 [Intacct] 圖格時，應該會自動登入您的 Intacct 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

