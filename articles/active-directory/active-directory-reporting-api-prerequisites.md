---
title: "存取 Azure AD 報告 API 的必要條件。 | Microsoft Docs"
description: "了解存取 Azure AD 報告 API 的必要條件"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0f23cc578fb946247d492b9dee92cf7a9daafa1


---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>存取 Azure AD 報告 API 的必要條件
[Azure AD 報告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 透過一組以 REST 為基礎的 API 為您提供資料的程式設計方式存取。 您可以從各種程式設計語言和工具呼叫這些 API。

報告 API 會使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授權存取 Web API。 

若要準備存取報告 API，您必須︰

1. 在 Azure AD 租用戶中建立應用程式 
2. 授與應用程式存取 Azure AD 資料的適當權限
3. 從您的目錄蒐集組態設定

如有相關疑問、問題或意見，請連絡 [AAD 報告協助](mailto:aadreportinghelp@microsoft.com)。

## <a name="create-an-azure-ad-application"></a>建立 Azure AD 應用程式
若要設定您的目錄以存取 Azure AD 報告 API，您必須以 Azure 訂用帳戶管理員帳戶登入 Azure 傳統入口網站，而且該帳戶同時也是 Azure AD 租用戶全域管理員目錄角色的成員。

> [!IMPORTANT]
> 像這樣使用具有「管理員」權限的認證所執行的應用程式會非常強大，請務必確保應用程式的識別碼/密碼認證的安全性。
> 
> 

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 從 **使用中目錄** 清單中，選取您的目錄。
3. 在頂端的功能表中，按一下 [應用程式] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/02.png) 
4. 按一下底列上的 [新增] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/03.png) 
5. 在 [欲執行動作] 對話方塊上，按一下 [新增我的組織正在開發的應用程式]。 
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/04.png) 
6. 在 [告訴我們您的應用程式]  對話方塊上，執行以下步驟： 
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/05.png) 
   
    a. 在 [名稱] 文字方塊中，輸入名稱 (例如：報告 API 應用程式)。
   
    b. 選取 [Web 應用程式和/或 Web API]。
   
    c. 按 [下一步] 。
7. 在 [應用程式屬性]  對話方塊上，執行下列步驟： 
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/06.png) 
   
    a. 在 [登入 URL] 文字方塊中，輸入 `https://localhost`。
   
    b. 在 [應用程式識別碼 URI] 文字方塊中，輸入 ```https://localhost/ReportingApiApp```。
   
    c. 按一下 [完成]。

## <a name="grant-your-application-permission-to-use-the-api"></a>授與您的應用程式使用 API 的權限
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 從 **使用中目錄** 清單中，選取您的目錄。
3. 在頂端的功能表中，按一下 [應用程式] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/02.png)
4. 在應用程式清單中，選取新建立的應用程式。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/07.png)
5. 在頂端的功能表中，按一下 [設定] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/08.png)
6. 在 [其他應用程式的權限] 區段中，若是 [Azure Active Directory] 資源，請按一下 [應用程式權限] 下拉式清單，然後選取 [讀取目錄資料]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/09.png)
7. 按一下底列上的 [儲存] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/10.png)

## <a name="gather-configuration-settings-from-your-directory"></a>從您的目錄蒐集組態設定
本節說明如何從您的目錄取得下列設定︰

* 網域名稱
* 用戶端識別碼
* 用戶端密碼

您在設定報告 API 的呼叫時需要這些值。 

### <a name="get-your-domain-name"></a>取得您的網域名稱
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 從 **使用中目錄** 清單中，選取您的目錄。
3. 在頂端的功能表中，按一下 [網域] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/11.png) 
4. 在 [網域名稱]  欄中，複製您的網域名稱。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/12.png) 

### <a name="get-the-applications-client-id"></a>取得應用程式的用戶端識別碼
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 從 **使用中目錄** 清單中，選取您的目錄。
3. 在頂端的功能表中，按一下 [應用程式] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/02.png) 
4. 在應用程式清單中，選取新建立的應用程式。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/07.png)
5. 在頂端的功能表中，按一下 [設定] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/08.png)
6. 複製您的 [用戶端識別碼] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/13.png)

### <a name="get-the-applications-client-secret"></a>取得應用程式的用戶端密碼
若要取得應用程式的用戶端密碼，您需要建立新的金鑰並在儲存新金鑰時儲存其值，因為稍後不可能再擷取此值。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 從 **使用中目錄** 清單中，選取您的目錄。
3. 在頂端的功能表中，按一下 [應用程式] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/02.png) 
4. 在應用程式清單中，選取新建立的應用程式。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/07.png)
5. 在頂端的功能表中，按一下 [設定] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/08.png)
6. 在 [金鑰]  區段中，執行下列步驟： 
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/14.png)
   
    a. 從持續時間清單中，選取持續時間。
   
    b. 按一下底列上的 [儲存] 。
   
    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/10.png)
   
    c. 複製金鑰值。

## <a name="next-steps"></a>後續步驟
* 您要以程式設計方式從 Azure AD 報告 API 存取資料嗎？ 請查看 [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。
* 如果您想要深入了解 Azure Active Directory 報告，請參閱 [Azure Active Directory 報告指南](active-directory-reporting-guide.md)。  




<!--HONumber=Dec16_HO5-->


