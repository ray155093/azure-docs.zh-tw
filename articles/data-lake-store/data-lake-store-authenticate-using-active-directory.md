---
title: "使用 Active Directory 驗證 Data Lake Store | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 驗證 Data Lake Store"
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
ms.date: 11/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 35cde786bbc091c58f4dcb341cd47ce4c4f4b46c
ms.openlocfilehash: 02e52c3aba82ab8e3a8b1dc921731c29e505e23e


---
# <a name="service-to-serivce-authentication-with-data-lake-store-using-azure-active-directory"></a>使用 Azure Active Directory 以 Data Lake Store 進行服務對服務驗證
> [!div class="op_single_selector"]
> * [服務對服務驗證](data-lake-store-authenticate-using-active-directory.md)
> * [使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store 使用 Azure Active Directory 進行驗證。 撰寫搭配 Azure Data Lake Store 或 Azure Data Lake Analytics 的應用程式之前，必須先決定要如何以 Azure Active Directory (Azure AD) 驗證應用程式。 兩個主要選項為︰

* 使用者驗證，以及 
* 服務對服務驗證。 

兩個選項都要靠 OAuth 2.0 權杖來提供您的應用程式，權杖會附加到每個對 Azure Data Lake Store 或 Azure Data Lake Analytics 提出的要求。

本文說明如何建立 Azure AD Web 應用程式以進行服務對服務驗證。 如需有關適用於使用者驗證的 Azure AD 應用程式組態的指示，請參閱[使用 Azure Active Directory 以 Data Lake Store 進行使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* 您的訂用帳戶識別碼。 您可以在 Azure 入口網站擷取。 例如，可從 [Data Lake Store] 帳戶刀鋒視窗取得。
  
    ![取得訂用帳戶識別碼](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)
* 您的 Azure AD 網域名稱。 將滑鼠游標停留在 Azure 入口網站右上角，即可擷取它。 在以下螢幕擷取畫面中，網域名稱是 **contoso.microsoft.com**，括號內的 GUID 是租用戶識別碼。 
  
    ![取得 AAD 網域](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="service-to-service-authentication"></a>服務對服務驗證
如果您希望應用程式自動向 Azure AD 驗證，不需要使用者提供其認證，這是建議的方法。 只要應用程式的認證仍有效，就可以自行驗證它本身，可依年序自訂認證。

### <a name="what-do-i-need-to-use-this-approach"></a>使用這個方法時需要什麼？
* Azure AD 網域名稱。 這已列在本文的必要條件中。
* Azure AD **Web 應用程式**。
* Azure AD Web 應用程式的用戶端識別碼。
* Azure AD Web 應用程式的用戶端密碼。
* Azure AD Web 應用程式的權杖端點。
* 啟用您想要使用的 Data Lake Store 檔案/資料夾或 Data Lake Analytics 帳戶上 Azure AD Web 應用程式的存取權。

如需有關如何建立 Azure AD Web 應用程式並針對上述需求進行設定的指示，請參閱下面的[建立 Active Directory 應用程式](#create-an-active-directory-application)一節。

> [!NOTE]
> Azure AD 應用程式預設是設定為使用用戶端密碼，您可以從 Azure AD 應用程式擷取此密碼。 不過，如果您希望 Azure AD 應用程式改用憑證，您就必須使用 Azure PowerShell 來建立 Azure AD Web 應用程式，如[使用憑證來建立服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)所述。
> 
> 

## <a name="create-an-active-directory-application"></a>建立 Active Directory 應用程式
在本節中，我們將了解如何建立和設定 Azure AD Web 應用程式，來使用 Azure Active Directory 以 Azure Data Lake Store 進行服務對服務驗證。 

### <a name="step-1-create-an-azure-active-directory-application"></a>步驟 1：建立 Azure Active Directory 應用程式
> [!NOTE]
> 下列步驟是使用 Azure 入口網站。 您也可以使用 [Azure PowerShell](../resource-group-authenticate-service-principal.md) 或 [Azure CLI](../resource-group-authenticate-service-principal-cli.md) 來建立 Azure AD 應用程式。
> 
> 

1. 透過 [傳統入口網站](https://manage.windowsazure.com/)登入 Azure 帳戶。
2. 從左窗格中，選取 [ **Active Directory** ]。
   
     ![選取 Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. 選取您想要用來建立新應用程式的 Active Directory。 如果您有多個 Active Directory，您通常會想要在訂用帳戶所在的目錄中建立應用程式。 您只能將訂用帳戶中資源的存取權授與和您訂用帳戶位於相同目錄的應用程式。  
   
     ![選擇目錄](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. 若要檢視目錄中的應用程式，請按一下 [ **應用程式**]。
   
     ![檢視應用程式](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. 如果您之前尚未在該目錄中建立應用程式，則應該會看到與下面類似的映像。 按一下 [加入應用程式]
   
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

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>步驟 3：將 Azure AD 應用程式指派給 Azure Data Lake Store 帳戶檔案或資料夾 (僅適用於服務對服務驗證)
1. 登入新的 [Azure 入口網站](https://portal.azure.com)，然後開啟要與您稍早建立的 Azure Active Directory 應用程式建立關聯的 Azure Data Lake Store 帳戶。
2. 在您的 [資料湖儲存區帳戶] 刀鋒視窗中，按一下 [資料總管] 。
   
    ![在資料湖儲存區帳戶中建立目錄](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Create directories in Data Lake account")
3. 在 [資料總管] 刀鋒視窗中，按一下您要將其存取權提供給 Azure AD 應用程式的檔案或資料夾，然後按一下 [存取]。 若要設定對檔案的存取權，您必須從 [檔案預覽] 刀鋒視窗按一下 [存取]。
   
    ![設定資料湖檔案系統上的 ACL](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Set ACLs on Data Lake file system")
4. [存取]  刀鋒視窗會列出已指派至根的標準存取和自訂存取。 按一下 [新增]  圖示以新增自訂層級的 ACL。
   
    ![列出標準和自訂存取](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "List standard and custom access")
5. 按一下 [新增] 圖示，以開啟 [新增自訂存取] 刀鋒視窗。 在此刀鋒視窗中，按一下 [選取使用者或群組]，然後在 [選取使用者或群組] 刀鋒視窗中，尋找您稍早建立的 Azure Active Directory 應用程式。 若您需要搜尋大量的群組，請使用頂端的文字方塊來篩選群組名稱。 按一下您要新增的群組，然後按一下 [選取] 。
   
    ![新增群組](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Add a group")
6. 按一下 [選取權限]，選取權限及權限的指派方式 (例如預設 ACL、存取 ACL 或兩者並用)。 按一下 [確定] 。
   
    ![將權限指派至群組](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Assign permissions to group")
   
    如需有關 Data Lake Store 中的權限及預設/存取 ACL 的詳細資訊，請參閱 [Data Lake Store 中的存取控制](data-lake-store-access-control.md)。
7. 在 [新增自訂存取] 刀鋒視窗中，按一下 [確定]。 具有相關權限的新增群組現在會列在 [存取]  刀鋒視窗中。
   
    ![將權限指派至群組](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Assign permissions to group")    

## <a name="next-steps"></a>後續步驟
在本文中，您已建立 Azure AD Web 應用程式，並收集您使用 .NET SDK、Java SDK 等撰寫的用戶端應用程式中所需的資訊。您現在可以繼續進行下列文章，這些文章說明如何使用 Azure AD Web 應用程式先以 Data Lake Store 進行驗證，然後再於存放區上執行其他作業。

* [使用 .NET SDK 開始使用 Azure 資料湖存放區](data-lake-store-get-started-net-sdk.md)
* [使用 Java SDK 開始使用 Azure Data Lake Store](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO5-->


