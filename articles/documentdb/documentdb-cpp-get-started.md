---
title: "DocumentDB 的 NoSQL C++ 教學課程 | Microsoft Docs"
description: "NoSQL C++ 教學課程，將使用 DocumentDB 背書的 C++ SDK 來建立 C++ 資料庫和主控台應用程式。 DocumentDB 是全球級的 NoSQL 資料庫服務。"
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 11/02/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8770e89cd54dae23ba05fb53382f7d89d19b029a


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>NoSQL C++ 教學課程：DocumentDB C++ 主控台應用程式
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
> 
> 

歡迎使用 Azure DocumentDB 背書的 C++ SDK 適用的 C++ 教學課程！ 完成本教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 DocumentDB 資源，包括 C++ 資料庫。

本文將討論：

* 建立和連接到 DocumentDB 帳戶
* 設定您的應用程式
* 建立 C++ DocumentDB 資料庫
* 建立集合
* 建立 JSON 文件
* 查詢集合
* 取代文件
* 刪除文件
* 刪除 C++ DocumentDB 資料庫

沒有時間嗎？ 別擔心！ 您可以在 [GitHub](https://github.com/stalker314314/DocumentDBCpp)上找到完整的方案。 請參閱 [取得完整的解決方案](#GetSolution) ，以取得簡要指示。

完成 C++ 教學課程之後，請使用此頁面底部的投票按鈕來提供意見。 

如果想要我們直接與您連絡，歡迎在留言中留下電子郵件地址或[在此與我們聯繫](https://www.research.net/r/8BKRJ3Z)。 

讓我們開始吧！

## <a name="prerequisites-for-the-c-tutorial"></a>C++ 教學課程的必要條件
請確定您具有下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費 Azure 試用](https://azure.microsoft.com/pricing/free-trial/)。
* [Visual Studio](https://www.visualstudio.com/downloads/)，並已安裝 C++ 語言元件。

## <a name="step-1-create-a-documentdb-account"></a>步驟 1：建立 DocumentDB 帳戶
讓我們建立 DocumentDB 帳戶。 如果您已經擁有想要使用的帳戶，就可以跳到 [設定您的 C++ 應用程式](#SetupNode)。

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupcastep-2-set-up-your-c-application"></a><a id="SetupC++"></a>步驟 2︰設定您的 C++ 應用程式
1. 開啟 Visual Studio，在 [檔案] 功能表上，按一下 [新增]，然後按一下 [專案]。 
2. 在 [新增專案] 視窗中，於 [已安裝] 窗格中展開 [Visual C++]，按一下 [Win32]，然後按一下 [Win32 主控台應用程式]。 將專案命名為 hellodocumentdb，然後按一下 [確定]。 
   
    ![新增專案精靈的螢幕擷取畫面](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. 當 Win32 應用程式精靈啟動時，按一下 [完成]。
4. 建立專案後，開啟 NuGet 套件管理員，方法是以滑鼠右鍵按一下 [方案總管] 中的 **hellodocumentdb** 專案，然後按一下 [管理 NuGet 套件]。 
   
    ![專案功能表上顯示管理 NuGet 套件的螢幕擷取畫面](media/documentdb-cpp-get-started/nuget.png)
5. 在 [NuGet: hellodocumentdb] 索引標籤上，按一下 [瀏覽]，然後搜尋「documentdbcpp」。 在結果中選取 DocumentDbCPP，如下列螢幕擷取畫面所示。 此套件會安裝 C++ REST SDK 的參考，該 SDK 是 DocumentDbCPP 的相依項目。  
   
    ![顯示已醒目提示 DocumentDbCpp 套件的螢幕擷取畫面](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    套件新增至您的專案後，一切便已準備就緒，可以開始撰寫一些程式碼。   

## <a name="a-idconfigastep-3-copy-connection-details-from-azure-portal-for-your-documentdb-database"></a><a id="Config"></a>步驟 3︰從 Azure 入口網站為您的 DocumentDB 資料庫複製連線詳細資料
讓 [Azure 入口網站](https://portal.azure.com)出現，並周遊至您所建立的 NoSQL (DocumentDB) 資料庫帳戶。 在下一個步驟中，我們需要從 Azure 入口網站取得的 URI 和主要金鑰，以便從我們的 C++ 程式碼片段建立連線。 

![Azure 入口網站中的 DocumentDB URI 和金鑰](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a name="a-idconnectastep-4-connect-to-a-documentdb-account"></a><a id="Connect"></a>步驟 4：連線至 DocumentDB 帳戶
1. 在原始程式碼的 `#include "stdafx.h"` 之後新增下列標頭和命名空間。
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. 接下來，在 main 函式中新增下列程式碼，並取代帳戶組態和主要金鑰，使其符合步驟 3 中的 DocumentDB 設定。 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    您現在有程式碼可初始化 documentdb，讓我們看看如何使用 DocumentDB 資源。

## <a name="a-idcreatedbcollastep-5-create-a-c-database-and-collection"></a><a id="CreateDBColl"></a>步驟 5︰建立 C++ 資料庫和集合
在執行此步驟之前，因為有人可能不熟悉 DocumentDB，我們先了解一下資料庫、集合和文件的互動方式。 [資料庫](documentdb-resources.md#databases)是分配到多個集合之文件儲存體的邏輯容器。 [集合](documentdb-resources.md#collections)是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。 您可以在 [DocumentDB 階層式資源模型和概念](documentdb-resources.md)中深入了解 DocumentDB 階層式資源模型和概念。

為了建立資料庫和對應的集合，請在 main 函式結尾新增下列程式碼。 這會使用您在上一個步驟中宣告的用戶端組態，建立名為「FamilyRegistry」的資料庫和名為「FamilyCollection」的集合。

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a name="a-idcreatedocastep-6-create-a-document"></a><a id="CreateDoc"></a>步驟 6：建立文件
[文件](documentdb-resources.md#documents)是使用者定義的 (任意) JSON 內容。 您現在可以將文件插入至 DocumentDB。 您可以將下列程式碼複製到 main 函式結尾，以建立文件。 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

總結來說，此程式碼會建立 DocumentDB 資料庫、集合和文件，而您可以在 Azure 入口網站的文件總管中進行查詢。 

![C++ 教學課程 - 說明帳戶、資料庫、集合和文件之間階層式關聯性的圖表](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a name="a-idquerydbastep-7-query-documentdb-resources"></a><a id="QueryDB"></a>步驟 7：查詢 DocumentDB 資源
DocumentDB 支援對儲存於每個集合的 JSON 文件進行 [豐富查詢](documentdb-sql-query.md) 。 下列範例程式碼示範使用 DocumentDB SQL 語法所建立的查詢，您可以針對我們在上一個步驟中建立的文件執行該查詢。

函式會採用資料庫和集合以及文件用戶端的唯一識別碼或資源識別碼來做為引數。 請在 main 函式之前新增此程式碼。

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-idreplaceastep-8-replace-a-document"></a><a id="Replace"></a>步驟 8：取代文件
DocumentDB 支援取代 JSON 文件，如下列程式碼的示範。 請在 executesimplequery 函式之後新增此程式碼。

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a name="a-iddeleteastep-9-delete-a-document"></a><a id="Delete"></a>步驟 9︰刪除文件
DocumentDB 支援刪除 JSON 文件，只要在 replacedocument 函式之後複製並貼上下列程式碼即可。 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-iddeletedbastep-10-delete-a-database"></a><a id="DeleteDB"></a>步驟 10︰刪除資料庫
刪除已建立的資料庫會移除資料庫和所有子系資源 (集合、文件等)。

在 deletedocument 函式之後複製並貼上下列程式碼片段 (cleanup 函式)，即可移除資料庫和所有子系資源。

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-idrunastep-11-run-your-c-application-all-together"></a><a id="Run"></a>步驟 11：一起執行您的 C++ 應用程式！
我們現已新增程式碼來建立、查詢、修改和刪除不同的 DocumentDB 資源。  現在讓我們將這一切連接起來，方法是從 hellodocumentdb.cpp 中的 main 函式對這些不同的函式新增呼叫以及一些診斷訊息。

若要這麼做，您可以使用下列程式碼取代應用程式的 main 函式。 這會覆寫您在步驟 3 中複製到程式碼的 account_configuration_uri 和 primary_key，因此請儲存該行，或從入口網站將這些值再次複製進來。 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

現在您應該能夠在 Visual Studio 中建置並執行您的程式碼，方法是按 F5 鍵，或是在終端機視窗中尋找應用程式並執行可執行檔。 

您應該可以看到入門應用程式的輸出。 該輸出應該會符合以下螢幕擷取畫面。

![DocumentDB C++ 應用程式輸出](media/documentdb-cpp-get-started/docdbconsole.png)

恭喜！ 您已完成 C++ 教學課程，並擁有您的第一個 DocumentDB 主控台應用程式！

## <a name="a-idgetsolutionaget-the-complete-c-tutorial-solution"></a><a id="GetSolution"></a>取得完整的 C++ 教學課程方案
若要建置包含本文中所有範例的 GetStarted 方案，您需要下列項目：

* [DocumentDB 帳戶][documentdb-create-account]。
* 您可以在 GitHub 上找到 [GetStarted](https://github.com/stalker314314/DocumentDBCpp) 方案。

## <a name="next-steps"></a>後續步驟
* 了解如何 [監視 DocumentDB 帳戶](documentdb-monitor-accounts.md)(英文)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中，針對範例資料集執行查詢。
* 如需深入了解程式設計模型，請參閱 [DocumentDB 文件頁面](https://azure.microsoft.com/documentation/services/documentdb/)中的＜開發＞一節。

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md






<!--HONumber=Nov16_HO2-->


