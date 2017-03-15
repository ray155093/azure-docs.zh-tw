---
title: "使用 MongoDB API 來建立 DocumentDB Web 應用程式 | Microsoft 文件"
description: "NoSQL 教學課程，使用 MongoDB 適用的 DocumentDB API 來建立線上資料庫 Web 應用程式。"
keywords: "mongodb 範例"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: c37304efecb2de247a2d63fe244e71c96966ebe0
ms.lasthandoff: 03/08/2017


---
# <a name="web-application-development-with-documentdb-api-for-mongodb"></a>使用 DocumentDB 開發 Web 應用程式：適用於 MongoDB 的 API
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [.NET for MongoDB](documentdb-mongodb-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

此範例將示範如何使用 .NET 來建立 DocumentDB：適用於 MongoDB 的 API Web 應用程式。

若要使用此範例，您必須︰

* [建立](documentdb-create-mongodb-account.md) Azure DocumentDB：適用於 MongoDB 的 API 帳戶。
* 擷取 MongoDB [連接字串](documentdb-connect-mongodb-account.md)資訊。

您可以使用[在連線至在虛擬機器上執行之 MongoDB 的 Azure 中建立 Web 應用程式](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md)教學課程，以最少的修改快速設定 MongoDB 應用程式 (在本機或發佈至 Azure Web 應用程式)，連線至 DocumentDB：適用於 MongoDB 的 API。  

1. 請遵循教學課程，只有一項修改。  使用下列項目取代 Dal.cs 程式碼：

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10250);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
   
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10250);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. 在您的每個帳戶設定中修改 Dal.cs 檔案中的下列變數：

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. 使用應用程式！

## <a name="next-steps"></a>後續步驟
* 了解如何[使用 MongoChef](documentdb-mongodb-mongochef.md) 和[使用 RoboMongo](documentdb-mongodb-robomongo.md) 來搭配 DocumentDB：適用於 MongoDB 的 API 帳戶。

