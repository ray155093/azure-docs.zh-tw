---
title: "使用 Node.js 的 Azure SDK 開始使用 Azure Data Lake Store | Microsoft Docs"
description: "了解如何利用 Node.js 來與 Data Lake Store 帳戶及檔案系統搭配使用。"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 2fee173c-69ae-4e1d-8773-48618cda9e16
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f33ccee7dd520adf074856616005c929040116dd
ms.openlocfilehash: 091ab246826c96b9d816c87b27014c1e54039429


---
# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>使用 Node.js 的 Azure SDK 開始使用 Azure Data Lake Store
> [!div class="op_single_selector"]
> * [入口網站](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

> [!NOTE]
> 若要上傳和下載大量資料 (大型檔案、大量檔案或兩者)，建議您使用 [Python SDK](data-lake-store-get-started-python.md)、[.NET SDK](data-lake-store-get-started-net-sdk.md) 或 [Azure PowerShell](data-lake-store-get-started-powershell.md)。 這些選項有較佳的效能，因為它們會使用多個執行緒平行處理資料移動。
> 
> 

了解如何使用 Node.js 的 Azure SDK 建立 Azure Data Lake Store 帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需有關 Data Lake Store 的詳細資訊，請參閱 [Data Lake Store 概觀](data-lake-store-overview.md)。 SDK 目前支援

* **Node.js 版本：0.10.0 或更高版本**
* **帳戶的 REST API 版本：2015-10-01-preview**
* **檔案系統的 REST API 版本：2015-10-01-preview**

## <a name="prerequisites"></a>必要條件
開始閱讀本文之前，您必須符合下列必要條件：

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **建立 Azure Active Directory 應用程式**。 您必須使用 Azure AD 應用程式來向 Azure AD 驗證 Data Lake Store 應用程式。 有不同的方法可向 Azure AD 進行驗證：**使用者驗證**或**服務對服務驗證**。 如需如何驗證的指示和詳細資訊，請參閱 [使用 Azure Active Directory 向 Data Lake Store 進行驗證](data-lake-store-authenticate-using-active-directory.md)。

## <a name="how-to-install"></a>如何安裝
```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>使用 Azure Active Directory 進行驗證
下列程式碼片段顯示兩個不同的方式來利用 Azure AD 驗證 Data Lake Store。 如需驗證 Data Lake Store 各種方法的詳細討論，請參閱[使用 Azure Active Directory 驗證 Data Lake Store](data-lake-store-authenticate-using-active-directory.md)。

下列程式碼片段也需要類似 Azure AD 網域名稱、Azure AD 應用程式的用戶端 ID 等等的輸入。所有這些詳細資料都可從您必須建立的 Azure AD 應用程式擷取，這些詳細資料也包含在上面的連結中。

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>建立 Data Lake Store 用戶端
```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>建立 Data Lake Store 帳戶
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="create-a-file-with-content"></a>建立具有內容的檔案
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>取得檔案和資料夾的清單
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>另請參閱
* [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK for Node.js - Data Lake Analytics 管理](https://www.npmjs.com/package/azure-arm-datalake-analytics)




<!--HONumber=Feb17_HO1-->


