---
title: "教學課程：Azure Active Directory 與 HR2day by Merces 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 HR2day by Merces 之間的單一登入"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 456ef3620c1d7bb6ab2bf09b094f977e46cf2ed6
ms.contentlocale: zh-tw
ms.lasthandoff: 06/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>教學課程：Azure Active Directory 與 HR2day by Merces 整合

在本教學課程中，您將了解如何整合 HR2day by Merces 與 Azure Active Directory (Azure AD)。

HR2day by Merces 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 HR2day by Merces 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 HR2day by Merces (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 HR2day by Merces 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 HR2day by Merces 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 HR2day by Merces
2. 設定並測試 Azure AD 單一登入

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>從資源庫新增 HR2day by Merces
若要設定將 HR2day by Merces 整合到 Azure AD 中，您需要從資源庫將 HR2day by Merces 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 HR2day by Merces，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **HR2day by Merces**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. 在結果窗格中，選取 [HR2day by Merces]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 HR2day by Merces 搭配運作的 Azure AD 單一登入。

為了讓單一登入能夠運作，Azure AD 必須知道 HR2day by Merces 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 HR2day by Merces 中的相關使用者之間建立連結關聯性。

在 HR2day by Merces 中，將**使用者名稱**的值指派為 Azure AD 中**使用者名稱**的值，以建立連結關聯性。

若要搭配 HR2day by Merces 來設定及測試 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 HR2day by Merces 測試使用者](#creating-an-hr2day-by-merces-test-user)** - 在 HR2day by Merces 中建立一個與 Azure AD 中代表 Britta Simon 的項目連結的 Britta Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 HR2day by Merces 應用程式中設定單一登入。

**若要搭配 HR2day by Merces 來設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [HR2day by Merces] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. 在 [HR2day by Merces 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://<tenantname>.force.com/<instancename>`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://hr2day.force.com/<companyname>`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [HR2day by Merces 用戶端支援小組](mailto:servicedesk@merces.nl)以取得這些值。 
 


4. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. 本節的目標是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠使用自己在 Azure AD 中的帳戶向 HR2day by Merces 進行驗證。

    HR2day by Merces 應用程式會預期要有特定格式的 SAML 判斷提示，這需要您將自訂屬性對應新增到您的 SAML 權杖屬性組態。 以下螢幕擷取畫面顯示上述的範例。 

    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    您必須先連絡 [HR2day by Merces 用戶端支援小組](mailto:servicedesk@merces.nl)，向其要求您租用戶的唯一識別碼屬性值，才能設定 SAML 判斷提示。 您需要這個值來完成下一節中的步驟：  

6. 在 [單一登入] 對話方塊的 [使用者屬性] 區段中，如圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
      | 屬性名稱    |   屬性值 |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | join([mail],"102938475Z","@" |
    
      a. 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b.這是另一個 C# 主控台應用程式。 在 [名稱] 文字方塊中，輸入 **“ATTR_LOGINCLAIM”**。

    c. 從 [值] 清單中，選取 [Join()]。

    d. 從 [String1] 清單中，選取 [user.mail]。

    e. 在 [String2] 清單中，輸入 HR2day 小組所提供的**唯一識別碼**。

    f. 在 [分隔符號] 文字方塊中，輸入 **@**。
    
    g. 按一下 [ **確定**]。

7. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. 在 [HR2day by Merces 組態] 區段上，按一下 [設定 HR2day by Merces] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. 若要為您的應用程式設定 SSO，請連絡您的 [HR2day by Merces 用戶端支援小組](mailTo:servicedesk@merces.nl)，並在電子郵件中附加所下載的 **Certificate(Base64)** 檔案。 同時也請提供 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]，讓這些項目能針對 SSO 整合來進行設定。

    > [!NOTE]
    > 請向 Merces 小組表明這項整合需要以下列模式設定「實體 ID」：**https://hr2day.force.com/INSTANCENAME**
    > 
    > 

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-an-hr2day-by-merces-test-user"></a>建立 HR2day by Merces 測試使用者

本節的目標是要在 HR2day by Merces 中建立名為 Britta Simon 的使用者。 請與 [HR2day by Merces 用戶端支援小組](mailto:servicedesk@merces.nl)合作，在 HR2day 帳戶中新增使用者。 

> [!NOTE]
> 如果您需要手動建立使用者，就需要連絡 [HR2day by Merces 用戶端支援小組](mailto:servicedesk@merces.nl)。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 HR2day by Merces 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 HR2day by Merces，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [HR2day by Merces] 。

    ![設定單一登入](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。  

當您在存取面板中按一下 [HR2day by Merces] 圖格時，應該會自動登入您的 HR2day by Merces 應用程式。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png


