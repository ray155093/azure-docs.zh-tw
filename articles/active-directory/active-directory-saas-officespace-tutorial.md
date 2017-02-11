---
title: "教學課程：Azure Active Directory 與 OfficeSpace Software 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 OfficeSpace Software 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 01e2bcf3fa32546a7952ddc919f99b46a74755a2
ms.openlocfilehash: e0933b1a7e19fc70f9c5e81225b296412837385e


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>教學課程：Azure Active Directory 與 OfficeSpace Software 整合

在本教學課程中，您將了解如何整合 OfficeSpace Software 與 Azure Active Directory (Azure AD)。

OfficeSpace Software 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 OfficeSpace Software 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 OfficeSpace Software (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 OfficeSpace Software 整合，您需要以下項目：

- Azure AD 訂用帳戶
- 啟用 OfficeSpace Software 單一登入的訂用帳戶


> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫加入 OfficeSpace Software
2. 設定並測試 Azure AD 單一登入


## <a name="adding-officespace-software-from-the-gallery"></a>從資源庫加入 OfficeSpace Software
若要設定 OfficeSpace Software 與 Azure AD 整合，您需要從資源庫將 OfficeSpace Software 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 OfficeSpace Software，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 

    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。

    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。

    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。

    ![應用程式][4]

6. 在搜尋方塊中輸入 **OfficeSpace Software**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

7. 在結果窗格中，選取 [OfficeSpace Software]，然後按一下 [完成] 以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 OfficeSpace Software 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 OfficeSpace Software 與 Azure AD 中互相對應的使用者。 換句話說，Azure AD 使用者和 OfficeSpace Software 中的相關使用者之間必須建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值指派為 OfficeSpace Software 中 **Username** 的值。

若要使用 OfficeSpace Software 設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 OfficeSpace Software 測試使用者](#creating-an-officespace-software-test-user)** - 使 OfficeSpace Software 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

本節目標是在 Azure 傳統入口網站啟用 Azure AD 單一登入，並在您的 OfficeSpace Software 應用程式中設定單一登入。

OfficeSpace Software 應用程式會預期要有特定格式的 SAML 判斷提示。 請設定此應用程式的下列宣告。 您可以從應用程式的 [屬性]索引標籤來管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。 

![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

**若要使用 OfficeSpace Software 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站中的 [OfficeSpace Software] 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。

    ![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

2. 在 [SAML Token 屬性]  對話方塊上，針對下表中顯示的每一列執行下列步驟：
    
    | 屬性名稱 | 屬性值 |
    | --- | --- |    
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | user.mail |
    | 電子郵件 | user.mail |
    | 名稱 | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. 按一下 [新增使用者屬性] 來開啟 [新增使用者屬性] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)
    
    b. 在 [屬性名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。
    
    c. 在 [屬性值] 清單中，輸入該資料列所顯示的屬性名稱。
    
    d. 按一下 [完成]

3. 在頂端的功能表中，按一下 [快速啟動] 。

    ![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png) 

4. 在傳統入口網站的 [OfficeSpace Software] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)

5. 在 [要如何讓使用者登入 OfficeSpace Software] 頁面上，選取 [Azure AD 單一登入]，然後按一下 [下一步]。
 
    ![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_06.png)

6. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_07.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<company name>.officespace.com/users/sign_in/saml`

    b.這是另一個 C# 主控台應用程式。 按 [下一步] 。

    > [!NOTE] 
    > 請注意，這不是真正的值。 您必須使用實際的「登入 URL」來更新此值。 請連絡 [OfficeSpace Software 支援小組](emaiLto:support@officespacesoftware.com)來取得這個值。

7. 於 **Configure single sign-on at OfficeSpace Software** (在 OfficeSpace Software 設定單一登入) 頁面上，按一下 [下載憑證]，然後將該檔案儲存在您的電腦上：

    ![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

8. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 OfficeSpace Software 租用戶。

9. 移至 **ADMIN**，然後按一下 [連接器]。

    ![在應用程式端設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

10. 按一下 [SAML 授權] 。

    ![在應用程式端設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

11. 在 [SAML 授權]  區段中，執行下列步驟：

    ![在應用程式端設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. 在 [登出提供者 URL] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [遠端登入 URL] 值。

    b.這是另一個 C# 主控台應用程式。 在 [用戶端 IdP 目標 URL] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [遠端登出 URL] 值。

    c. 從下載的憑證複製**指紋**值，然後將它貼至 [用戶端 IDP 憑證指紋] 文字方塊中。 

    d. 按一下 [儲存設定] 。

    > [!NOTE]
    > 如需詳細資訊，請參閱 [如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)

12. 在傳統入口網站中，選取單一登入組態確認，然後按 [下一步] 。

    ![Azure AD 單一登入][10]

13. 在 [單一登入確認] 頁面上，按一下 [完成]。  
  
    ![Azure AD 單一登入][11]


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/create_aaduser_09.png) 

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/create_aaduser_05.png) 

    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c. 按 [下一步] 。

6.  在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/create_aaduser_06.png) 

    a. 在 [名字] 文字方塊中，輸入 **Britta**。  

    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。

    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d. 在 [角色] 清單中選取 [使用者]。

    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-officespace-tutorial/create_aaduser_08.png) 

    a. 記下 [新密碼] 的值。

    b. 按一下頁面底部的 [新增] 。   



### <a name="creating-an-officespace-software-test-user"></a>建立 OfficeSpace Software 測試使用者

本節目標是在 OfficeSpace Software 中建立名為 Britta Simon 的使用者。 OfficeSpace Software 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 嘗試存取 OfficeSpace Software 時，如果使用者還不存在，就會建立新使用者。

> [!NOTE]
> 如果您需要手動建立使用者，您需要連絡 [OfficeSpace Software 支援小組](emaiLto:support@officespacesoftware.com)。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 OfficeSpace Software 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要指派 Britta Simon 到 OfficeSpace Software，請執行以下步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [OfficeSpace Software]。

    ![設定單一登入](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. 在頂端的功能表中，按一下 [使用者] 。

    ![指派使用者][203] 

4. 在 [使用者] 清單中，選取 [Britta Simon] 。

5. 在底部的工具列中，按一下 [指派] 。
    
    ![指派使用者][205]



### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在 [存取面板] 中按一下 [OfficeSpace Software] 圖格時，您應該會自動登入您的 OfficeSpace Software 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


