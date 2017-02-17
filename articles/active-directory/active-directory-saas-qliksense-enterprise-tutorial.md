---
title: "教學課程：Azure Active Directory 與 Qlik Sense Enterprise 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Qlik Sense Enterprise 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 00dcedb09ea63b4337b2b730746fb2f5a1f27e64


---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>教學課程：Azure Active Directory 與 Qlik Sense Enterprise 整合
在本教學課程中，您會了解如何整合 Qlik Sense Enterprise 與 Azure Active Directory (Azure AD)。

Qlik Sense Enterprise 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Qlik Sense Enterprise 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Qlik Sense Enterprise (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 Qlik Sense Enterprise 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 Qlik Sense Enterprise 單一登入的訂用帳戶

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

1. 從資源庫新增 Qlik Sense Enterprise
2. 設定並測試 Azure AD 單一登入

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>從資源庫新增 Qlik Sense Enterprise
若要設定將 Qlik Sense Enterprise 整合到 Azure AD 中，您需要從資源庫將 Qlik Sense Enterprise 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Qlik Sense Enterprise，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]

6. 在搜尋方塊中輸入 **Qlik Sense Enterprise**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. 在結果窗格中選取 [Qlik Sense Enterprise]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Qlik Sense Enterprise 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Qlik Sense Enterprise 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Qlik Sense Enterprise 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中的 [使用者名稱] 的值指定為 Qlik Sense Enterprise 中 **Username** 的值。

若要設定及測試與 Qlik Sense Enterprise 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Qlik Sense Enterprise 測試使用者](#creating-a-qliksense-enterprise-test-user)** - 在 Qlik Sense Enterprise 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，並在您的 Qlik Sense Enterprise 中設定單一登入。

**若要使用 Qlik Sense Enterprise 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在傳統入口網站的 [Qlik Sense Enterprise] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 

2. 在 [要如何讓使用者登入 Qlik Sense Enterprise] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 
   
    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入使用者用來登入 Qlik Sense Enterprise 應用程式的 URL：**https://\<Qlik Sense 完整主機名稱\>:443/<虛擬 Proxy 前置詞\>/samlauthn/**。
   
    > [!NOTE]
    > 請注意此 URI 的尾端斜線。  這是必要的。
    > 
    > 
   
    b. 按 [下一步] 

4. 在 [設定在 Qlik Sense Enterprise 單一登入]  頁面上，執行下列步驟︰
   
    ![設定單一登入](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)
   
    a. 按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。  將此中繼資料檔案上傳到 Qlik Sense 伺服器之前，請先準備好編輯該檔案。
   
    b. 按一下頁面底部的 [新增] 。

5. 準備同盟中繼資料 XML 檔案，以便將該檔案上傳至 Qlik Sense 伺服器。
   
    > [!NOTE]
    > 將 IdP 中繼資料上傳至 Qlik Sense 伺服器之前，必須先編輯此檔案才能移除資訊，以確保 Azure AD 與 Qlik Sense 伺服器之間的運作正常。
    > 
    > 
   
    ![QlikSense][qs24]
   
    a. 在文字編輯器中開啟從 Azure 下載的 FederationMetaData.xml 檔案。
   
    b. 搜尋 **RoleDescriptor** 值。  將會有四個項目 (兩對開頭和結尾元素標籤)。
   
    c. 從檔案中刪除 RoleDescriptor 標籤與其間的所有資訊。
   
    d. 將此檔案存放在附近，以便稍後用於本文件。
6. 以可建立虛擬 Proxy 組態的使用者身分，瀏覽至 Qlik Sense Qlik 管理主控台 (QMC)。
7. 在 QMC 中，按一下 [虛擬 Proxy] 功能表項目。
   
    ![QlikSense][qs6] 
8. 按一下畫面底部的 [建立新的]。
   
    ![QlikSense][qs7]
9. 虛擬 Proxy 編輯畫面隨即出現。  畫面右邊的功能表會顯示組態選項。
   
    ![QlikSense][qs9]
10. 勾選 [識別] 功能表選項後，輸入 Azure 虛擬 Proxy 組態的識別資訊。
    
    ![QlikSense][qs8]  
    
    a. [說明] 欄位是虛擬 Proxy 組態的易記名稱。  輸入說明的值。
    
    b. [前置詞] 欄位可識別虛擬 Proxy 端點，以便連接到採用 Azure AD 單一登入的 Qlik Sense。  輸入此虛擬 Proxy 的獨特前置詞名稱。
    
    c. [工作階段閒置逾時 (分鐘)] 是指透過此虛擬 Proxy 連接的逾時值。
    
    d. [工作階段 cookie 標頭名稱] 是 cookie 名稱，用於儲存使用者在成功驗證後收到的 Qlik Sense 工作階段識別碼。  此名稱必須是唯一的。
11. 按一下 [驗證] 功能表選項，讓它立即可見。  [驗證] 畫面隨即出現。
    
    ![QlikSense][qs10]
    
    a. [匿名存取模式] 下拉式清單可讓您決定匿名使用者是否可透過虛擬 Proxy 存取 Qlik Sense。  預設選項是 [沒有匿名使用者]。
    
    b. [驗證方法] 下拉式清單可讓您決定虛擬 Proxy 將要使用的驗證配置。  從下拉式清單選取 [SAML]。  因此會顯示更多選項。
    
    c. 在 [SAML 主機 URI] 欄位中，輸入使用者必須輸入才可透過此 SAML 虛擬 Proxy 存取 Qlik Sense 的主機名稱。  主機名稱是 Qlik Sense 伺服器的 URI。
    
    d. 在 [SAML 實體識別碼] 中，輸入與 [SAML 主機 URI] 欄位相同的值。
    
    e. [SAML IdP 中繼資料] 是稍早在 [從 Azure AD 組態編輯同盟中繼資料] 區段中編輯的檔案。  **上傳 IdP 中繼資料之前，必須先編輯此檔案**才能移除資訊，以確保 Azure AD 與 Qlik Sense 伺服器之間的運作正常。  **如果檔案尚未進行編輯，請參閱上述指示。**  如果檔案已經過編輯，請按一下 [瀏覽] 按鈕，然後選取已編輯的中繼資料檔案，將它上傳至虛擬 Proxy 組態。
    
    f. 輸入 SAML 屬性的屬性名稱或結構描述參考，代表 Azure AD 將傳送至 Qlik Sense 伺服器的 **UserID**。  在設定後的 Azure 應用程式畫面中可取得結構描述參考資訊。  若要使用名稱屬性，**請輸入 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
    
    g. 輸入**使用者目錄**的值，此值會在使用者透過 Azure AD 向 Qlik Sense 伺服器進行驗證時附加至使用者。  硬式編碼值必須以**方括號 []** 括住。  若要使用 Azure AD SAML 判斷提示中傳送的屬性，請在此文字方塊中輸入屬性名稱 (**不需**方括號)。
    
    h. [SAML 簽章演算法] 可設定虛擬 Proxy 組態的服務提供者 (在此例中是 Qlik Sense 伺服器) 憑證簽署。  如果 Qlik Sense 伺服器使用透過 Microsoft Enhanced RSA 和 AES 密碼編譯提供者產生的受信任憑證，請將 SAML 簽署演算法變更為 **SHA-256**。
    
    i. [SAML 屬性對應] 區段可讓其他屬性 (如群組) 傳送到 Qlik Sense，以便用於安全性規則。
12. 按一下 [負載平衡] 功能表選項，讓它立即可見。  [負載平衡] 畫面隨即出現。
    
    ![QlikSense][qs11]
13. 按一下 [新增伺服器節點] 按鈕，選取 Qlik Sense 為了達到負載平衡而會將工作階段傳送至的引擎節點，然後按一下 [新增] 按鈕。
    
    ![QlikSense][qs12]
14. 按一下 [進階] 功能表選項，讓它立即可見。 [進階] 畫面隨即出現。
    
    ![QlikSense][qs13]
    
    a. [主機允許清單] 會識別在連接到 Qlik Sense 伺服器時所接受的主機名稱。  **輸入連接到 Qlik Sense 伺服器時使用者會指定的主機名稱。** 主機名稱是與 SAML 主機 uri 相同的值 (不含 https://)。
15. 按一下 [套用] 按鈕。
    
    ![QlikSense][qs14]
16. 按一下 [確定] 以接受警告訊息，該訊息指出將重新啟動連結至虛擬 Proxy 的 Proxy。
    
    ![QlikSense][qs15]
17. 畫面右側會出現 [相關聯的項目] 功能表。  按一下 [Proxy] 功能表選項。
    
    ![QlikSense][qs16]
18. Proxy 畫面隨即出現。  按一下底部的 [連結] 按鈕，將 Proxy 連結到虛擬 Proxy。
    
    ![QlikSense][qs17]
19. 選取將會支援此虛擬 Proxy 連線的 Proxy 節點，然後按一下 [連結] 按鈕。  連結之後，Proxy 會列在相關聯的 Proxy 之下。
    
    ![QlikSense][qs18]
    ![QlikSense][qs19]
20. 大約五到十秒後，[重新整理 QMC] 訊息會出現。  按一下 [重新整理 QMC] 按鈕。
    
    ![QlikSense][qs20]
21. 當 QMC 重新整理時，按一下 [虛擬 Proxy] 功能表項目。 新的 SAML 虛擬 Proxy 項目會列在螢幕上的表格中。  按一下此虛擬 Proxy 項目。
    
    ![QlikSense][qs51]
22. 畫面底部的 [下載 SP 中繼資料] 按鈕將會啟用。  按一下 [下載 SP 中繼資料] 按鈕，將中繼資料儲存至檔案。
    
    ![QlikSense][qs52]
23. 開啟 SP 中繼資料檔案。  觀察 **entityID** 項目和 **AssertionConsumerService** 項目。  這些值相當於 Azure AD 應用程式組態中的 [識別碼] 和 [登入 URL]。 如果不相符，您應該在 Azure AD 應用程式組態精靈中加以取代。
    
    ![QlikSense][qs53]
24. 在傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。
    
    ![Azure AD 單一登入][10]
25. 在 [單一登入確認] 頁面上，按一下 [完成]。  
    
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟： ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按 [下一步] 。

6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。
   
    e. 按 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。   

### <a name="creating-an-qlik-sense-enterprise-test-user"></a>建立 Qlik Sense Enterprise 測試使用者
在本節中，您要在 Qlik Sense Enterprise 中建立名為 Britta Simon 的使用者。 請與 Qlik Sense Enterprise 支援小組合作，在 JQlik Sense Enterprise 平台中新增使用者。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會將 Qlik Sense Enterprise 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Qlik Sense Enterprise，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Qlik Sense Enterprise] 。
   
    ![設定單一登入](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]

4. 在 [使用者] 清單中，選取 [Britta Simon] 。

5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

## <a name="testing-single-sign-on"></a>測試單一登入
在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Qlik Sense Enterprise] 圖格時，應該會自動登入您的 Qlik Sense Enterprise 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png



<!--HONumber=Feb17_HO3-->


