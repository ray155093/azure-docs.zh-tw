---
title: "教學課程：Azure Active Directory 與 Cisco Spark 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Cisco Spark 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 931004d458fd0cbf3e002805ca701d777ddc57c2
ms.openlocfilehash: f0f12b1667b7f45fd164fac658502c93e8afb855
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>教學課程：Azure Active Directory 與 Cisco Spark 整合
在本教學課程中，您會了解如何整合 Cisco Spark 與 Azure Active Directory (Azure AD)。

將 Cisco Spark 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Cisco Spark 的人員
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Cisco Spark 單一登入 (SSO)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Cisco Spark 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 **Cisco Spark** 單一登入 (SSO) 的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Cisco Spark
2. 設定並測試 Azure AD SSO

## <a name="add-cisco-spark-from-the-gallery"></a>從資源庫新增 Cisco Spark
若要設定將 Cisco Spark 整合到 Azure AD 中，您需要從資源庫將 Cisco Spark 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Cisco Spark，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **Cisco Spark**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)
7. 在結果窗格中，選取 [Cisco Spark]，然後按一下 [完成] 新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Cisco Spark 設定和測試 Azure AD SSO。

若要讓 SSO 運作，Azure AD 必須知道 Cisco Spark 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Cisco Spark 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值，指派為 Cisco Spark 中 [使用者名稱] 的值。 若要設定和測試對 Cisco Spark 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Cisco Spark 測試使用者](#creating-a-cisco-spark-test-user)** - 使 Cisco Spark 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO
本節目標是在 Azure 傳統入口網站啟用 Azure AD SSO，並在您的 Cisco Spark 應用程式中設定單一登入功能。

Cisco Spark 應用程式預期 SAML 判斷提示會包含特定屬性。 設定此應用程式的下列屬性。 您可以從應用程式的 [屬性]  索引標籤來管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。

![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**若要設定與 Cisco Spark 搭配運作的 Azure AD SSO，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 **Cisco Spark** 應用程式整合頁面上，按一下頂端功能表中的 [屬性]。
   
    ![設定單一登入][5]
2. 在 [SAML Token 屬性]  對話方塊中，執行下列步驟：
  1. 按一下 [新增使用者屬性] 來開啟 [新增使用者屬性] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
  2. 在 [屬性名稱] 文字方塊中，輸入 **uid**。
  3. 在 [屬性值] 清單中，選取 [user.userprincipal]。
  4. 按一下頁面底部的 [新增] 。 然後，按一下頁面底部的 [套用變更]。
3. 按一下頂端功能表中的 [快速啟動] 。
   
    ![設定單一登入][6]
4. 在傳統入口網站的 [Cisco Spark] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][7] 
5. 在 [要如何讓使用者登入 Cisco Spark] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)
6. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)
  1. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://web.ciscospark.com/#/signin`。
  2. 按 [下一步] 。
7. 在 [設定在 Cisco Spark 單一登入] 頁面上，按一下 [下載中繼資料]，然後將檔案儲存在您的電腦中。
   
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)
8. 使用完整系統管理員認證，登入 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。
9. 選取 [設定]，然後按一下 [驗證] 區段下方的 [修改]。
   
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
9. 選取 [整合協力廠商識別提供者 (進階)]，然後移至下一個畫面。
10. 按一下 [下載中繼資料檔案]，然後將檔案儲存在您的電腦上。
11. 在 [匯入 Idp 中繼資料] 頁面上，將 Azure AD 中繼資料檔案拖放到頁面，或使用檔案瀏覽器選項來找到並上傳 Azure AD 中繼資料檔案。 然後，選取 [需要中繼資料中憑證授權單位所簽署的憑證 (較安全)]，然後按 [下一步]。 
   
   ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)
12. 選取 [測試 SSO 連接]，並在新的瀏覽器索引標籤開啟時，透過登入向 Azure AD 進行驗證。
13. 返回 [Cisco Cloud Collaboration Management] 瀏覽器索引標籤。 如果測試成功，請選取 [這項測試成功。啟用單一登入] 選項，然後按 [下一步]。
14. 在 Azure AD 傳統入口網站上，選取單一登入設定確認，然後按一下 [下一步] 。
   
    ![Azure AD 單一登入][10]
15. 在 [單一登入確認] 頁面上，按一下 [完成]。  
    
     ![Azure AD 單一登入][11]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 
  1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
  2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
  3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 
  1. 在 [名字] 文字方塊中，輸入 **Britta**。  
  2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
  3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
  4. 在 [角色] 清單中選取 [使用者]。
  5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 
  1. 記下 [新密碼] 的值。
  2. 按一下 [完成]。   

### <a name="create-a-cisco-spark-test-user"></a>建立 Cisco Spark 測試使用者
在本節中，您要在 Cisco Spark 中建立名為 Britta Simon 的使用者。 在本節中，您要在 Cisco Spark 中建立名為 Britta Simon 的使用者。

1. 使用完整系統管理員認證，移至 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。
2. 按一下 [使用者]，然後按一下 [管理使用者]。
   
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 
3. 在 [管理使用者] 視窗中，選取 [手動新增或修改使用者]，然後按 [下一步]。
4. 選取 [名稱和電子郵件地址]。 然後，如下填寫文字方塊︰
   
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
  1. 在 [名字] 文字方塊中，輸入 **Britta**。 
  2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
  3. 在 [電子郵件地址] 文字方塊中，輸入 **britta.simon@contoso.com**。
5. 按一下加號以新增 Britta Simon。 然後按 [下一步] 。
6. 在 [新增使用者的服務] 視窗中，按一下 [儲存]，然後按一下 [完成]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會將 Cisco Spark 的存取權授與 Britta Simon，讓她能夠使用 Azure SSO。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Cisco Spark，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [Cisco Spark]。
   
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [所有使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD SSO 組態。

當您按一下存取面板中的 [Cisco Spark] 圖格時，應該會自動登入您的 Cisco Spark 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png

