---
title: "使用 MongoDB API 建置 Azure Cosmos DB 應用程式 | Microsoft Docs"
description: "使用適用於 MongoDB 的 DocumentDB API 建立線上資料庫的教學課程。"
keywords: "mongodb 範例"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d4243fc1400c4e9ad545060f253ae46a579e406f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>使用 Node.js 建置 Azure Cosmos DB：適用於 MongoDB 的 API 應用程式
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js for MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
>

此範例將示範如何使用 Node.js 建置 Azure Cosmos DB：適用於 MongoDB 的 API 主控台應用程式。

若要使用此範例，您必須︰

* [建立](documentdb-create-mongodb-account.md) Azure Cosmos DB：適用於 MongoDB 的 API 帳戶。
* 擷取 MongoDB [連接字串](documentdb-connect-mongodb-account.md)資訊。

## <a name="create-the-app"></a>建立應用程式

1. 建立 *app.js* 檔案，複製並貼上下列程式碼。

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```

2. 在 *app.js* 中根據每個帳戶設定 (了解如何尋找您的[連接字串](documentdb-connect-mongodb-account.md)) 修改下列變數：
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
    ```
     
3. 開啟您最愛的終端機，執行 **npm install mongodb --save**，然後使用 **node app.js** 執行您的應用程式

## <a name="next-steps"></a>後續步驟
* 了解如何[使用 MongoChef](documentdb-mongodb-mongochef.md) 搭配您的 Azure Cosmos DB：適用於 MongoDB 的 API 帳戶。

