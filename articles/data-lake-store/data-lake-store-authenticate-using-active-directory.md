---
title: "服務對服務驗證︰使用 Azure Active Directory 以 Data Lake Store 進行 | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 以 Data Lake Store 完成服務對服務驗證"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: dac6c9f3be7b4535f8cb30a9ec0c1e398ca5ff28


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>使用 Azure Active Directory 以 Data Lake Store 進行服務對服務驗證
> [!div class="op_single_selector"]
> * [服務對服務驗證](data-lake-store-authenticate-using-active-directory.md)
> * [使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store 使用 Azure Active Directory 進行驗證。 撰寫搭配 Azure Data Lake Store 或 Azure Data Lake Analytics 的應用程式之前，必須先決定要如何以 Azure Active Directory (Azure AD) 驗證應用程式。 兩個主要選項為︰

* 使用者驗證 
* 服務對服務驗證 (本文) 

兩個選項都要靠 OAuth 2.0 權杖來提供您的應用程式，權杖會附加到每個對 Azure Data Lake Store 或 Azure Data Lake Analytics 提出的要求。

本文說明如何建立 Azure AD Web 應用程式以進行服務對服務驗證。 如需有關適用於使用者驗證的 Azure AD 應用程式組態的指示，請參閱[使用 Azure Active Directory 以 Data Lake Store 進行使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-an-active-directory-application"></a>建立 Active Directory 應用程式
在本節中，我們將了解如何建立和設定 Azure AD Web 應用程式，來使用 Azure Active Directory 以 Azure Data Lake Store 進行服務對服務驗證。 請注意，建立「Active Directory 應用程式」，實際上不會建立應用程式或任何程式碼，而是建立您的服務主體。

### <a name="step-1-create-an-azure-active-directory-application"></a>步驟 1：建立 Azure Active Directory 應用程式
1. 透過 [傳統入口網站](https://manage.windowsazure.com/)登入 Azure 帳戶。
2. 從左窗格中，選取 [ **Active Directory** ]。
   
     ![選取 Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. 選取您想要用來建立新應用程式的 Active Directory。 如果您有多個 Active Directory，您通常會想要在訂用帳戶所在的目錄中建立應用程式。 您只能將訂用帳戶中資源的存取權授與和您訂用帳戶位於相同目錄的應用程式。  
   
     ![選擇目錄](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. 若要檢視目錄中的應用程式，請按一下 [ **應用程式**]。
   
     ![檢視應用程式](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. 如果您之前尚未在該目錄中建立應用程式，則應該會看到類似下圖。 按一下 [加入應用程式]
   
     ![新增應用程式](./media/data-lake-store-authenticate-using-active-directory/create-application.png)
   
     或者，按一下下方窗格中的 [ **新增** ]。
   
     ![新增](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)
6. 提供應用程式的名稱，然後選取您想要建立的應用程式類型。 針對本教學課程，建立 [Web 應用程式和/或 Web API]  ，然後按 [下一步] 按鈕。
   
     ![名稱應用程式](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

    > [!TIP]
    > 提供比較容易搜尋的應用程式名稱。 在本教學課程稍後，您將搜尋此應用程式，並將它指派給 Data Lake Store 帳戶。
    > 
    > 

7. 填寫您應用程式的屬性。 針對 [登入 URL]，提供描述您應用程式的網站 URI。 這並不會驗證網站是否存在。 
   針對 [應用程式識別碼 URI]，提供識別您應用程式的 URI。
   
     ![應用程式屬性](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)
   
    按一下核取記號以完成精靈步驟並建立應用程式。

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>步驟 2：取得用戶端識別碼、用戶端密碼及權杖端點
以程式設計方式登入時，您需要應用程式的識別碼。 如果應用程式是在自己的認證下執行，則您還需要一個驗證金鑰。

1. 按一下 [設定] 索引標籤來設定您應用程式的密碼。
   
     ![設定應用程式](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)
2. 複製 [用戶端識別碼] 。
   
     ![用戶端識別碼](./media/data-lake-store-authenticate-using-active-directory/client-id.png)
3. 如果應用程式將在自己的認證下執行，請向下捲動至 [金鑰] 區段，然後選取您想要的密碼有效期限。
   
     ![金鑰](./media/data-lake-store-authenticate-using-active-directory/create-key.png)
4. 選取 [ **儲存** ] 建立金鑰。
   
    ![儲存](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)
   
    即會顯示儲存的金鑰，而且您可以進行複製。 您將無法在稍後擷取金鑰，因此必須現在複製它。
   
    ![儲存的金鑰](./media/data-lake-store-authenticate-using-active-directory/save-key.png)
5. 選取畫面底部的 [檢視端點]，然後擷取 [OAuth 2.0 權杖端點] 欄位的值來擷取權杖端點，如以下所示。  
   
    ![租用戶識別碼](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

#### <a name="note-down-the-following-properties-that-you-will-need-for-the-next-steps"></a>請將後續步驟所需的下列屬性記下：
1. 在上述步驟 1.6 建立的 Web 應用程式識別碼名稱
2. 在上述步驟 2.2 所擷取的用戶端識別碼
3. 在上述步驟 2.4 所建立的金鑰
4. 在上述步驟 2.5 所擷取的租用戶識別碼

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>步驟 3：將 Azure AD 應用程式指派給 Azure Data Lake Store 帳戶檔案或資料夾 (僅適用於服務對服務驗證)
1. 登入新的 [Azure 入口網站](https://portal.azure.com)，然後開啟要與您稍早建立的 Azure Active Directory 應用程式建立關聯的 Azure Data Lake Store 帳戶。
2. 在您的 [資料湖儲存區帳戶] 刀鋒視窗中，按一下 [資料總管] 。
   
    ![在 Data Lake Store 帳戶中建立目錄](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "在 Data Lake Store 帳戶中建立目錄")
3. 在 [資料總管] 刀鋒視窗中，按一下您要將其存取權提供給 Azure AD 應用程式的檔案或資料夾，然後按一下 [存取]。 若要設定對檔案的存取權，您必須從 [檔案預覽] 刀鋒視窗按一下 [存取]。
   
    ![設定 Data Lake 檔案系統上的 ACL](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "設定 Data Lake 檔案系統上的 ACL")
4. [存取]  刀鋒視窗會列出已指派至根的標準存取和自訂存取。 按一下 [新增]  圖示以新增自訂層級的 ACL。
   
    ![列出標準和自訂存取](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "列出標準和自訂的存取")
5. 按一下 [新增] 圖示，以開啟 [新增自訂存取] 刀鋒視窗。 在此刀鋒視窗中，按一下 [選取使用者或群組]，然後在 [選取使用者或群組] 刀鋒視窗中，尋找您稍早建立的 Azure Active Directory 應用程式。 若您需要搜尋大量的群組，請使用頂端的文字方塊來篩選群組名稱。 按一下您要新增的群組，然後按一下 [選取] 。
   
    ![加入群組](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "加入群組")
6. 按一下 [選取權限]，選取權限及權限的指派方式 (例如預設 ACL、存取 ACL 或兩者並用)。 按一下 [確定] 。
   
    ![將權限指派至群組](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "將權限指派至群組")
   
    如需有關 Data Lake Store 中的權限及預設/存取 ACL 的詳細資訊，請參閱 [Data Lake Store 中的存取控制](data-lake-store-access-control.md)。
7. 在 [新增自訂存取] 刀鋒視窗中，按一下 [確定]。 具有相關權限的新增群組現在會列在 [存取]  刀鋒視窗中。
   
    ![將權限指派至群組](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "將權限指派至群組")    

## <a name="next-steps"></a>後續步驟
在本文中，您已建立 Azure AD Web 應用程式，並收集您使用 .NET SDK、Java SDK 等撰寫的用戶端應用程式中所需的資訊。您現在可以繼續進行下列文章，這些文章說明如何使用 Azure AD Web 應用程式先以 Data Lake Store 進行驗證，然後再於存放區上執行其他作業。

* [使用 .NET SDK 開始使用 Azure 資料湖存放區](data-lake-store-get-started-net-sdk.md)
* [使用 Java SDK 開始使用 Azure Data Lake Store](data-lake-store-get-started-java-sdk.md)

本文章會逐步引導您取得使用者主體和執行應用程式所需的基本步驟。 您可以查看下列文章以取得進一步資訊︰
* [使用 PowerShell 建立服務主體](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用憑證驗證進行服務主體驗證](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [向 Azure AD 驗證的其他方法](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)





<!--HONumber=Jan17_HO4-->


