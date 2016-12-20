---
title: "使用 Active Directory 驗證 Data Lake Store | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 驗證 Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7e7c9fd2fe7e6327cd39c4c26583c8fd556c9044


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>使用 Azure Active Directory 以 Data Lake Store 進行使用者驗證
> [!div class="op_single_selector"]
> * [服務對服務驗證](data-lake-store-authenticate-using-active-directory.md)
> * [使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store 使用 Azure Active Directory 進行驗證。 撰寫搭配 Azure Data Lake Store 或 Azure Data Lake Analytics 的應用程式之前，必須先決定要如何以 Azure Active Directory (Azure AD) 驗證應用程式。 兩個主要選項為︰

* 使用者驗證，以及 
* 服務對服務驗證。 

兩個選項都要靠 OAuth 2.0 權杖來提供您的應用程式，權杖會附加到每個對 Azure Data Lake Store 或 Azure Data Lake Analytics 提出的要求。

本文說明如何建立 Azure AD Web 應用程式以進行使用者驗證。 如需服務對服務驗證的 Azure AD 應用程式組態的指示，請參閱[使用 Azure Active Directory 以 Data Lake Store 進行服務對服務驗證](data-lake-store-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* 您的訂用帳戶識別碼。 您可以在 Azure 入口網站擷取。 例如，可從 [Data Lake Store] 帳戶刀鋒視窗取得。
  
    ![取得訂用帳戶識別碼](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* 您的 Azure AD 網域名稱。 將滑鼠游標停留在 Azure 入口網站右上角，即可擷取它。 在以下螢幕擷取畫面中，網域名稱是 **contoso.microsoft.com**，括號內的 GUID 是租用戶識別碼。 
  
    ![取得 AAD 網域](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>使用者驗證
如果您想要讓使用者透過 Azure AD 登入您的應用程式，建議使用這個方法。 您的應用程式將能夠存取 Azure 資源，並與登入使用者具有相同的存取層級。 您的使用者必須定期提供認證，您的應用程式才能繼續存取。

有使用者登入的結果，是系統會給您的應用程式一個存取權杖和一個重新整理權杖。 存取權杖會附加到每個對 Data Lake Store 或 Data Lake Analytics 提出的要求，預設的有效期是一小時。 重新整理權杖可用來取得新的存取權杖，預設的有效期是二小時，如果定期使用則最多兩週。 您可以使用兩種不同方法讓使用者登入。

### <a name="using-the-oauth-20-pop-up"></a>使用 OAuth 2.0 快顯視窗
您的應用程式可以觸發 OAuth 2.0 授權快顯視窗，讓使用者輸入其認證。 如有必要，這個快顯視窗也適用於 Azure AD 雙因素驗證 (2FA) 程序。 

> [!NOTE]
> 適用於 Python 或 Java 的 Azure AD 驗證程式庫 (ADAL) 尚未支援這個方法。
> 
> 

### <a name="directly-passing-in-user-credentials"></a>直接傳遞使用者認證
您的應用程式可以直接提供對 Azure AD 的使用者認證。 這個方法僅適用於有組織識別碼的使用者帳戶；與個人/「即時識別碼」使用者帳戶不相容，包括以 @outlook.com 或 @live.com. 結尾的帳戶。此外，這個方法與需要 Azure AD 雙因素驗證 (2FA) 的使用者帳戶不相容。

### <a name="what-do-i-need-to-use-this-approach"></a>使用這個方法時需要什麼？
* Azure AD 網域名稱。 已列在本文的先決條件中。
* Azure AD **Web 應用程式**
* Azure AD Web 應用程式的用戶端識別碼
* Azure AD Web 應用程式的回覆 URI
* 設定委派權限

如需如何建立 Azure AD Web 應用程式，並且針對上述需求設定它的指示，請參閱以下的[建立 Active Directory 應用程式](#create-an-active-directory-application)章節。 

## <a name="create-an-active-directory-application"></a>建立 Active Directory 應用程式
在本節中，我們了解如何建立和設定 Azure AD Web 應用程式，使用 Azure Active Directory 以 Azure Data Lake Store 進行使用者驗證。

### <a name="step-1-create-an-azure-active-directory-application"></a>步驟 1：建立 Azure Active Directory 應用程式
> [!NOTE]
> 下列步驟是使用 Azure 入口網站。 您也可以使用 [Azure PowerShell](../resource-group-authenticate-service-principal.md) 或 [Azure CLI](../resource-group-authenticate-service-principal-cli.md) 來建立 Azure AD 應用程式。
> 
> 

1. 透過 [傳統入口網站](https://manage.windowsazure.com/)登入 Azure 帳戶。
2. 從左窗格中，選取 [ **Active Directory** ]。
   
     ![選取 Active Directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. 選取您想要用來建立新應用程式的 Active Directory。 如果您有多個 Active Directory，您通常會想要在訂用帳戶所在的目錄中建立應用程式。 您只能將訂用帳戶中資源的存取權授與和您訂用帳戶位於相同目錄的應用程式。  
   
     ![選擇目錄](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. 若要檢視目錄中的應用程式，請按一下 [ **應用程式**]。
   
     ![檢視應用程式](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. 如果您之前尚未在該目錄中建立應用程式，則應該會看到與下面類似的映像。 按一下 [加入應用程式]
   
     ![新增應用程式](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     或者，按一下下方窗格中的 [ **新增** ]。
   
     ![新增](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. 提供應用程式的名稱，然後選取您想要建立的應用程式類型。 針對本教學課程，建立 [Web 應用程式和/或 Web API]  ，然後按 [下一步] 按鈕。
   
     ![名稱應用程式](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. 填寫您應用程式的屬性。 針對 [登入 URL]，提供描述您應用程式的網站 URI。 這並不會驗證網站是否存在。 
   針對 [應用程式識別碼 URI]，提供識別您應用程式的 URI。
   
     ![應用程式屬性](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    按一下核取記號以完成精靈並建立應用程式。

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>步驟 2︰取得用戶端識別碼、回覆 URI，並設定委派的權限
1. 按一下 [設定] 索引標籤設定您應用程式的密碼。
   
     ![設定應用程式](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. 複製 [用戶端識別碼] 。
   
     ![用戶端識別碼](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. 在 [單一登入] 區段中，複製 [回覆 URI]。
   
    ![用戶端識別碼](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. 在 [其他應用程式的權限] 底下，按一下 [新增應用程式]
   
    ![用戶端識別碼](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. 在 [其他應用程式的權限] 精靈中，選取 [Azure Data Lake] 和 [Windows Azure 服務管理 API]，然後按一下核取記號。
6. 根據預設，新增服務的**委派的權限**設定為零。 按一下 Azure Data Lake 和 Windows Azure 管理服務的 [委派的權限] 下拉式清單，然後選取可用的核取方塊，以將值設定為 1。 結果應該如下所示。
   
     ![用戶端識別碼](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. 按一下 [儲存] 。

## <a name="next-steps"></a>後續步驟
在本文中，您已建立 Azure AD Web 應用程式，並收集您使用 .NET SDK、Java SDK 等撰寫的用戶端應用程式中所需的資訊。您現在可以繼續進行下列文章，這些文章說明如何使用 Azure AD Web 應用程式先以 Data Lake Store 進行驗證，然後再於存放區上執行其他作業。

* [使用 .NET SDK 開始使用 Azure 資料湖存放區](data-lake-store-get-started-net-sdk.md)
* [使用 Java SDK 開始使用 Azure Data Lake Store](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO3-->


