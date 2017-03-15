---
title: "教學課程：Azure Active Directory 與 M-Files 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 M-Files 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 78f0a065b675326a9a507e0cf480bc779fd458a9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>教學課程：Azure Active Directory 與 M-Files 整合
在本教學課程中，您將了解如何整合 M-Files 與 Azure Active Directory (Azure AD)。

M-Files 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 M-Files 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 M-Files 單一登入 (SSO)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定與 M-Files 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 **M-Files** SSO 的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 M-Files
2. 設定並測試 Azure AD SSO

## <a name="adding-m-files-from-the-gallery"></a>從資源庫新增 M-Files
若要設定 M-Files 與 Azure AD 整合，您需要從資源庫將 M-Files 加入到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 M-Files，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **M-Files**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_01.png)
7. 在結果窗格中，選取 [M-Files]，然後按一下 [完成] 以新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 M-Files 設定及測試 Azure AD SSO。

若要讓 SSO 運作，Azure AD 必須知道 M-Files 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 M-Files 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 M-Files 中 **Username** 的值。 若要設定及測試與 M-Files 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 M-Files 測試使用者](#creating-a-m-file-test-user)** - 在 M-Files 中建立一個 Britta Simon 對應項目，其要與 Azure AD 中代表她的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-sso"></a>設定 Azure AD SSO
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD SSO，並在您的 M-Files 應用程式中設定 SSO。

**若要設定與 M-Files 搭配運作的 Azure AD SSO，請執行下列步驟：**

1. 在傳統入口網站的 [M-Files] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][7] 
2. 在 [要如何讓使用者登入 M-Files] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_06.png)
3. 在 [設定 App 設定]  對話方塊頁面執行下列步驟：
   
    ![設定單一登入](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_07.png)
  1. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://<tenant-name>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`
  2. 按 [下一步] 。
4. 在 [設定在 M-Files 單一登入] 頁面上，按一下 [下載中繼資料]，然後將檔案儲存在您的電腦中。
   
    ![設定單一登入](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_09.png)
5. 若要取得為您的應用程式設定的 SSO，請透過 <mailto:support@m-files.com> 連絡您的 M-files 支援小組並提供下載的中繼資料。
   
   >[!NOTE]
   >如果您想為您的 M-File 傳統型應用程式設定 SSO，請依照下列步驟執行。 如果您只想為 M-Files 的 Web 版本設定 SSO，則不需要任何額外步驟。  
   > 
6. 請依照下列步驟設定 M-File 傳統型應用程式，以搭配 Azure AD 啟用 SSO。 若要下載 M-Files，請移至 [M-Files 下載](https://www.m-files.com/en/download-latest-version)頁面。
7. 開啟 [M-Files 桌面設定] 視窗。 然後按一下 [新增]。
   
    ![設定單一登入](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)
8. 在 [文件保存庫連線內容] 視窗中，執行下列步驟︰
   
  ![設定單一登入](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  
    在 [伺服器] 區段中，輸入以下的值︰  
 1. 在 [名稱] 中，輸入 `<tenant-name>.cloudvault.m-files.com`。  
 2. 在 [連接埠號碼]中，輸入 **4466**。 
 3. 在 [通訊協定]中，選取 **HTTPS**。 
 4. 在 [驗證] 欄位中，選取 [特定的 Windows 使用者]。 然後，系統會提示您簽署頁面。 請插入您的 Azure AD 認證。 
 5. 在 [伺服器上的保存庫]，選取伺服器上對應的保存庫。 
 6. 按一下 [確定] 。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/create_aaduser_09.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/create_aaduser_05.png) 
 1. 針對 [使用者類型]，選取 [您組織中的新使用者]。  
 2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。 
 3. 按 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/create_aaduser_06.png)  
 1. 在 [名字] 文字方塊中，輸入 **Britta**。   
 2. 在 [姓氏] 文字方塊中，輸入 **Simon**。 
 3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。 
 4. 在 [角色] 清單中選取 [使用者]。 
 5. 按 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-m-files-tutorial/create_aaduser_08.png)  
 1. 記下 [新密碼] 的值。 
 2. 按一下頁面底部的 [新增] 。   

### <a name="create-a-m-files-test-user"></a>建立 M-Files 測試使用者
在本節中，您要在 M-Files 中建立名為 Britta Simon 的使用者。 如果您不知道如何在 M-Files 中建立使用者，請連絡 M-Files 支援 (<mailto:support@m-files.com>)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會將 M-Files 的存取權授與 Britta Simon，讓她能夠使用 Azure SSO。

![指派使用者][200]

**若要將 Britta Simon 指派給 M-Files，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [M-Files]。 
   
    ![設定單一登入](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_12.png)
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [所有使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD SSO 組態。

當您在存取面板中按一下 [M-Files] 磚時，應該會自動登入您的 M-Files 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-m-files-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_205.png

