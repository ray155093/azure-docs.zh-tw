---
title: 適用於 MongoDB 的 DocumentDB 範例 | Microsoft Docs
description: 尋找 DocumentDB 的 MongoDB 通訊協定支援的範例。
keywords: mongodb 範例
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: anhoh

---
# <a name="documentdb-protocol-support-for-mongodb-examples"></a>MongoDB 的 DocumentDB 通訊協定支援範例
若要使用這些範例，您必須︰

* [建立](documentdb-create-mongodb-account.md) 具有 MongoDB 的通訊協定支援的 Azure DocumentDB 帳戶。
* 擷取具有 MongoDB 通訊協定支援的 DocumentDB 帳戶的 [連接字串](documentdb-connect-mongodb-account.md) 資訊。

## <a name="get-started-with-a-sample-asp.net-mvc-task-list-application"></a>開始使用範例 ASP.NET MVC 工作清單應用程式
您可以使用 [在連接至在虛擬機器上執行之 MongoDB 的 Azure 中建立 Web 應用程式](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) 教學課程，以最少的修改快速設定 MongoDB 應用程式 (在本機或發佈至 Azure Web 應用程式)，連接到具有 MongoDB 通訊協定支援的 DocumentDB 帳戶。  

1. 請遵循教學課程，只有一項修改。  使用下列項目取代 Dal.cs 程式碼：
   
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
2. 在您的每個帳戶設定中修改 Dal.cs 檔案中的下列變數：
   
       private string userName = "<your user name>";
       private string host = "<your host>";
       private string password = "<your password>";
3. 使用應用程式！

## <a name="next-steps"></a>後續步驟
* 了解如何 [使用 MongoChef](documentdb-mongodb-mongochef.md) 和具有 MongoDB 通訊協定支援的 DocumentDB 帳戶。

<!--HONumber=Oct16_HO2-->


