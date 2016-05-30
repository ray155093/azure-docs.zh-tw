<properties 
   pageTitle="使用 Azure SDK for Node.js 管理 Azure Data Lake Store | Microsoft Azure"
   description="了解如何管理資料湖存放區帳戶以及檔案系統。" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/21/2016"
   ms.author="nitinme"/>

# 使用 Node.js 的 Azure SDK 管理 Azure 資料湖存放區

> [AZURE.SELECTOR]
- [入口網站](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)


Azure SDK for Node.js 可用於管理 Azure Data Lake Store 帳戶以及檔案系統作業。

它目前支援︰

  *  **Node.js 版本：0.10.0 或更高版本**
  *  **帳戶的 REST API 版本：2015-10-01-preview**
  *  **檔案系統的 REST API 版本：2015-10-01-preview**

## 特性

- 帳戶管理：建立、取得、列出、更新及刪除。
- 檔案系統管理：建立、取得、上傳、附加、下載、讀取、刪除、列出。

## 如何安裝

```bash
npm install azure-arm-datalake-store
```

## 使用 Azure Active Directory 進行驗證

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## 建立 Data Lake Analytics 用戶端

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, 'your-subscription-id');
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials, 'azuredatalakestore.net');
```

## 建立 Data Lake Store 帳戶

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

## 建立具有內容的檔案
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.filesystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## 取得檔案和資料夾的清單

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.filesystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## 另請參閱

- [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node)
- [Microsoft Azure SDK for Node.js - Data Lake Analytics 管理](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

<!---HONumber=AcomDC_0518_2016-->