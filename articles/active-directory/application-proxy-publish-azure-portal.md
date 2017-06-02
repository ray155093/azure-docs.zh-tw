---
title: "使用 Azure AD 應用程式 Proxy 發佈應用程式 | Microsoft Docs"
description: "在 Azure 入口網站中使用 Azure AD 應用程式 Proxy 將內部部署應用程式發佈至雲端。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: a6dc6148c3307d41801a4c1112397f3e63006ae8
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017

---


# <a name="publish-applications-using-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 發佈應用程式

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-proxy-publish-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-application-proxy-publish.md)

Azure Active Directory (AD) 應用程式 Proxy 可藉由發佈要透過網際網路存取的內部部署應用程式，協助您支援遠端背景工作角色。 透過 Azure 入口網站，您可以發佈在區域網路上執行的應用程式，並提供從您的網路外部的安全遠端存取。

本文會逐步引導您完成使用應用程式 Proxy 發佈內部部署應用程式。 完成本文之後，您便可以使用單一登入、個人化的資訊或安全性需求開始設定應用程式。

如果您是應用程式 Proxy 的新手，可至[如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)深入了解這個功能。


## <a name="publish-an-on-premises-app-for-remote-access"></a>發佈可遠端存取的內部部署應用程式


> [!TIP]
> 如果這是您第一次使用應用程式 Proxy 時，請選擇已設定為密碼型驗證的應用程式。 應用程式 Proxy 支援其他類型的驗證，但密碼型應用程式是開始使用並快速執行最簡單的方式。 

1. 在 [Azure 入口網站](https://portal.azure.com/)中，以系統管理員身分登入。
2. 選取 [Azure Active Directory]  >  [企業應用程式]  >  [新增應用程式]。

  ![新增企業應用程式](./media/application-proxy-publish-azure-portal/add-app.png)

3. 在 [類別] 頁面上，選取 [內部部署應用程式]。  

  ![新增您自己的應用程式](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 提供您的應用程式的下列資訊：

   - **名稱**︰會出現在存取面板上的應用程式名稱。 

   - **內部 URL**：應用程式 Proxy 連接器用來從您的私人網路存取應用程式的位址。 您可以提供後端伺服器上要發佈的特定路徑，而伺服器的其餘部分則不發佈。 如此一來，您可以在相同的伺服器上將不同網站發佈為不同應用程式，並給予各自的名稱和存取規則。

     > [!TIP]
     > 如果您發佈路徑，請確定其中包含您的應用程式的所有必要映像、指令碼和樣式表。 例如，如果您的應用程式位於 https://yourapp/app 並使用位於 https://yourapp/media 的映像，您應該發佈 https://yourapp/ 做為路徑。

   - **外部 URL**：您的使用者要前往此位址才能在您的網路之外存取您的應用程式。
   - **預先驗證**︰應用程式 Proxy 在給予您的應用程式存取權前，用來驗證使用者的方式。 

     - Azure Active Directory︰應用程式 Proxy 會重新導向使用者以使用 Azure AD 登入，進而驗證目錄和應用程式的權限。 我們建議將此選項設為預設。
     - 即時通行︰使用者不必向 Azure Active Directory 進行驗證即可存取應用程式。 您還是可以在後端設定驗證需求。
   - **轉譯標頭中的 URL?**︰選擇是要轉譯標頭中的 URL，或保留原始 URL。 
   - **連接器群組**︰連接器會處理針對應用程式的遠端存取，連接器群組可協助您依區域、網路或用途組織連接器和應用程式。 如果您尚未建立任何連接器群組，您的應用程式會指派給 [預設] 群組，且您會看到請您[建立連接器群組](active-directory-application-proxy-connectors-azure-portal.md)的警告訊息。

   ![設定您的應用程式](./media/application-proxy-publish-azure-portal/configure-app.png)

8. 選取 [新增] 。


## <a name="add-a-test-user"></a>新增測試使用者 

若要測試您的應用程式是否已正確發行，可新增一個您具有存取權的使用者帳戶。 

1. 在 [快速啟動] 刀鋒視窗中，選取 [指派測試使用者]。

  ![指派測試使用者](./media/application-proxy-publish-azure-portal/assign-user.png)

2. 在 [使用者和群組] 刀鋒視窗上，選取 [新增]。

  ![新增使用者或群組](./media/application-proxy-publish-azure-portal/add-user.png)

3. 在 [新增] 指派刀鋒視窗上，選取 [使用者和群組]，然後選擇您要新增的帳戶。 
4. 選取 [指派]。

## <a name="test-your-published-app"></a>測試已發佈的應用程式

在瀏覽器中瀏覽至您在發行步驟所設定的外部 URL。 您應該會看到開始畫面，且能夠使用您設定的測試帳戶登入。

![測試已發佈的應用程式](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>後續步驟
- [下載連接器](active-directory-application-proxy-enable.md)和[建立連接器群組](active-directory-application-proxy-connectors-azure-portal.md)，在不同的網路和位置上發佈應用程式。

- 為您新發行的應用程式[設定單一登入](application-proxy-sso-azure-portal.md)

