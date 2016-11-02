<properties
   pageTitle="使用 Active Directory 驗證 Data Lake Store | Microsoft Azure"
   description="了解如何使用 Azure Active Directory 驗證 Data Lake Store"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>


# <a name="authenticate-with-data-lake-store-using-azure-active-directory"></a>使用 Azure Active Directory 驗證 Data Lake Store

Azure Data Lake Store 使用 Azure Active Directory 進行驗證。 撰寫搭配 Azure Data Lake Store 或 Azure Data Lake Analytics 的應用程式之前，必須先決定要如何以 Azure Active Directory (Azure AD) 驗證應用程式。 兩個主要選項為︰

* 使用者驗證，以及 
* 服務對服務驗證。 

兩個選項都要靠 OAuth 2.0 權杖來提供您的應用程式，權杖會附加到每個對 Azure Data Lake Store 或 Azure Data Lake Analytics 提出的要求。


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* 您的訂用帳戶識別碼。 您可以在 Azure 入口網站擷取。 例如，可從 [Data Lake Store] 帳戶刀鋒視窗取得。

    ![取得訂用帳戶識別碼](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)

* 您的 Azure AD 網域名稱。 將滑鼠游標停留在 Azure 入口網站右上角，即可擷取它。 

    ![取得 AAD 網域](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>使用者驗證

如果您想要讓使用者透過 Azure AD 登入您的應用程式，建議使用這個方法。 您的應用程式將能夠存取 Azure 資源，並與登入使用者具有相同的存取層級。 您的使用者必須定期提供認證，您的應用程式才能繼續存取。

有使用者登入的結果，是系統會給您的應用程式一個存取權杖和一個重新整理權杖。 存取權杖會附加到每個對 Data Lake Store 或 Data Lake Analytics 提出的要求，預設的有效期是一小時。 重新整理權杖可用來取得新的存取權杖，預設的有效期是二小時，如果定期使用則最多兩週。 您可以使用兩種不同方法讓使用者登入。

### <a name="using-the-oauth-2.0-pop-up"></a>使用 OAuth 2.0 快顯視窗

您的應用程式可以觸發 OAuth 2.0 授權快顯視窗，讓使用者輸入其認證。 如有必要，這個快顯視窗也適用於 Azure AD 雙因素驗證 (2FA) 程序。 

>[AZURE.NOTE] 適用於 Python 或 Java 的 Azure AD 驗證程式庫 (ADAL) 尚未支援這個方法。

### <a name="directly-passing-in-user-credentials"></a>直接傳遞使用者認證

您的應用程式可以直接提供對 Azure AD 的使用者認證。 這個方法僅適用於有組織識別碼的使用者帳戶；與個人/「即時識別碼」使用者帳戶不相容，包括以 @outlook.com 或 @live.com. 結尾的帳戶。此外，這個方法與需要 Azure AD 雙因素驗證 (2FA) 的使用者帳戶不相容。

### <a name="what-do-i-need-to-use-this-approach?"></a>使用這個方法時需要什麼？

* Azure AD 網域名稱 (已列在本文的＜先決條件＞中)。

* Azure AD **原生用戶端應用程式**。 

* Azure AD 原生用戶端應用程式的用戶端識別碼。

* Azure AD 原生用戶端應用程式的重新導向 URI。 

如需有關如何建立 Azure AD 應用程式及擷取用戶端識別碼的指示，請參閱 [建立 Active Directory 應用程式](../resource-group-create-service-principal-portal.md#create-an-active-directory-application)。 

>[AZURE.NOTE] 以上連結中的指示適用於 Azure AD Web 應用程式。 不過，即使您選擇改為建立原生用戶端應用程式，步驟也完全相同。

## <a name="service-to-service-authentication"></a>服務對服務驗證

如果您希望應用程式自動向 Azure AD 驗證，不需要使用者提供其認證，這是建議的方法。 只要應用程式的認證仍有效，就可以自行驗證它本身，可依年序自訂認證。

### <a name="what-do-i-need-to-use-this-approach?"></a>使用這個方法時需要什麼？

* Azure AD 網域名稱 (已列在本文的＜先決條件＞中)。

* Azure AD **Web 應用程式**。

* Azure AD Web 應用程式的用戶端識別碼。

    >[AZURE.NOTE] 如需有關如何建立 Azure AD 應用程式及擷取用戶端識別碼的指示，請參閱 [建立 Active Directory 應用程式](../resource-group-create-service-principal-portal.md#create-an-active-directory-application)。
    
* 將 Azure AD Web 應用程式設定為使用用戶端密碼或使用憑證。 若要建立使用憑證的 Web 應用程式，請參閱 [建立有憑證的服務主體](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)。

* 啟用您想要使用的 Data Lake Store 檔案/資料夾或 Data Lake Analytics 帳戶上 Azure AD Web 應用程式的存取權。 如需有關如何向 Data Lake Store 檔案/資料夾提供 Azure AD 應用程式存取權的詳細資訊，請參閱 [將使用者或安全性群組以 ACL 形式指派給 Azure Data Lake Store 檔案系統](data-lake-store-secure-data.md#filepermissions)。

## <a name="next-steps"></a>後續步驟

- [使用 .NET SDK 開始使用 Azure 資料湖存放區](data-lake-store-get-started-net-sdk.md)
- [使用 Java SDK 開始使用 Azure Data Lake Store](data-lake-store-get-started-java-sdk.md)



<!--HONumber=Oct16_HO2-->


