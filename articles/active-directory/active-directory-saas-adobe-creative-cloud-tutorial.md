---
title: "教學課程：Azure Active Directory 與 Adobe Creative Cloud 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Adobe Creative Cloud 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9ba1171e-56b1-4475-b308-58637d35e5a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 3d13608612c77236346b0e98551d7fc427d602e1
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>教學課程：Azure Active Directory 與 Adobe Creative Cloud 整合

在本教學課程中，您會了解如何整合 Adobe Creative Cloud 與 Azure Active Directory (Azure AD)。

Adobe Creative Cloud 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Adobe Creative Cloud 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Adobe Creative Cloud (單一登入)
- 您可以在 Azure 管理入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Adobe Creative Cloud 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Adobe Creative Cloud 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Adobe Creative Cloud
2. 設定並測試 Azure AD 單一登入

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>從資源庫新增 Adobe Creative Cloud
若要設定將 Adobe Creative Cloud 整合至 Azure AD 中，您需要從資源庫將 Adobe Creative Cloud 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Adobe Creative Cloud，請執行下列步驟：**

1. 在 **[Azure 管理入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 [Adobe Creative Cloud]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_000.png)

5. 在結果窗格中，選取 [Adobe Creative Cloud]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Adobe Creative Cloud 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Adobe Creative Cloud 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Adobe Creative Cloud 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，是將 Azure AD 中**使用者名稱**的值指派為 Adobe Creative Cloud 中 **Username** 的值。

若要設定及測試與 Adobe Creative Cloud 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Adobe Creative Cloud 測試使用者](#creating-an-adobe-creative-cloud-test-user)** - 在 Adobe Creative Cloud 中，建立一個與 Azure AD 中代表 Britta Simon 的項目連結的 Britta Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 管理入口網站中啟用 Azure AD 單一登入，並在您的 Adobe Creative Cloud 應用程式中設定單一登入。

**若要設定與 Adobe Creative Cloud 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 管理入口網站的 [Adobe Creative Cloud] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，選取 [SAML 型登入] 做為 [模式]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_01.png)

3. 如果您想要以 **IDP** 起始模式設定應用程式，請在 [Adobe Creative Cloud 網域和 URL] 區段上執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url1.png)

    a. 在 [識別碼] 文字方塊中，以下列形式輸入值：`https://www.okta.com/saml2/service-provider/<token>`

    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > 請注意這些不是真正的值。 您必須使用實際的識別碼和回覆 URL 更新這些值。 在此建議您在 [識別碼] 中使用唯一的字串值。 如果您需要手動建立使用者，請連絡 Adobe Creative Cloud 支援小組。

4. 如果您想要以  起始模式設定應用程式，請在 [Adobe Creative Cloud 網域和 URL] 區段上執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url2.png)

    a. 按一下 [顯示進階 URL 設定] 選項

    b. 在 [登入 URL] 文字方塊中，輸入下列值：`https://adobe.com`

5. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_05.png) 

6. 在 [Adobe Creative Cloud 組態] 區段中，按一下 [設定 Adobe Creative Cloud] 以開啟 [設定登入] 視窗。 請從 [快速參考] 區段複製 [SAML 實體 ID] 和 [SAML SSO 服務 URL]。

    ![設定單一登入](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_06.png) 

7. 在不同的網頁瀏覽器視窗中，以管理員身分登入您的 Adobe Creative Cloud 租用戶。

8.  移至左側瀏覽窗格上的 [身分識別]，然後按一下您的網域。 接著執行 [需要單一登入組態] 區段中的下列步驟。

    ![設定](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "設定")

9. 按一下 [瀏覽]，將已從 Azure AD 下載的憑證上傳至 **IDP 憑證**。

10. 在 [IDP 簽發者] 文字方塊中，放入 [SAML 實體 ID] 的值，該值是您從 Azure 入口網站的 [設定登入] 區段複製而來。

11. 在 [IDP 登入 URL] 文字方塊中，放入 [SAML SSO 服務 URL] 的值，該值是您從 Azure 入口網站的 [設定登入] 區段複製而來。

12. 選取 [HTTP - 重新導向] 作為 **IDP 繫結**。

13. 選取 [電子郵件地址] 作為**使用者登入設定**。
 
14. 按一下 [儲存]  按鈕。

15. 儀表板現在會顯示 XML「下載中繼資料」檔案。 它包含 Adobe 的 EntityDescriptor URL 和 AssertionConsumerService URL。 請開啟檔案，然後在 Azure AD 應用程式中加以設定。

    ![在應用程式端設定單一登入](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![在應用程式端設定單一登入](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. 在 [設定應用程式設定] 對話方塊上，使用 Adobe 提供給您的 EntityDescriptor 值作為**識別碼**。

    b. 在 [設定應用程式設定] 對話方塊上，使用 Adobe 提供給您的 AssertionConsumerService 值作為**回覆 URL**。
 
### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 管理入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png) 

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。 

### <a name="creating-an-adobe-creative-cloud-test-user"></a>建立 Adobe Creative Cloud 測試使用者

若要讓 Azure AD 使用者能夠登入 Adobe Creative Cloud，必須將他們佈建到 Adobe Creative Cloud。  
Adobe Creative Cloud 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Adobe Creative Cloud 公司網站。

2. 按一下 [人員] 。

    ![人員](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_001.png "人員")

3. 按一下 [邀請使用者] 。

    ![邀請使用者](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_002.png "邀請使用者")

4. 在 [邀請人員] 對話方塊頁面上，執行下列步驟：

    ![邀請人員](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_003.png "邀請人員")

    a. 在 [電子郵件] 文字方塊中，輸入 Britta Simon 帳戶的電子郵件地址。
    
    b. 按一下 [邀請] 。

    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Adobe Creative Cloud 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Adobe Creative Cloud，請執行下列步驟：**

1. 在 Azure 管理入口網站中，開啟應用程式檢視，然後瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Adobe Creative Cloud]。

    ![設定單一登入](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_50.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Adobe Creative Cloud] 圖格時，應該會自動登入您的 Adobe Creative Cloud 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png
