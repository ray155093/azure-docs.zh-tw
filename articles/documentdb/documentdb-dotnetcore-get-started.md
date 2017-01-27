---
title: "NoSQL 教學課程：DocumentDB .NET Core SDK | Microsoft Docs"
description: "NoSQL 教學課程，將使用 DocumentDB .NET Core SDK 來建立線上資料庫以及 C# 主控台應用程式。 DocumentDB 是 JSON 的 NoSQL 資料庫。"
services: documentdb
documentationcenter: .net
author: arramac
manager: jhubbard
editor: 
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 3cbfe3c0ad08990d5c41630ebacc47432b2c6307
ms.openlocfilehash: 31b4635c1350428f589b708543ffb64f2a456b0b


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application-on-net-core"></a>NoSQL 教學課程：在 .NET Core 上建置 DocumentDB C# 主控台應用程式
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

歡迎使用 Azure DocumentDB .NET Core SDK 的 NoSQL 教學課程！ 完成本教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 DocumentDB 資源。

本文將討論：

* 建立和連接到 DocumentDB 帳戶
* 設定 Visual Studio 方案
* 建立線上資料庫
* 建立集合
* 建立 JSON 文件
* 查詢集合
* 取代文件
* 刪除文件
* 刪除資料庫

沒有時間嗎？ 別擔心！ 您可以在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)上找到完整的方案。 請跳至 [取得完整的解決方案](#GetSolution) 一節，以取得簡要指示。

之後，請使用此頁面頂端或底部的投票按鈕，將意見反應提供給我們。 如果想要我們直接與您連絡，歡迎在留言中留下電子郵件地址。

> [!NOTE]
> 本教學課程中使用的 DocumentDB .NET Core SDK 尚未與通用 Windows 平台 (UWP) 應用程式相容。 如需可支援 UWP 應用程式的 .NET Core SDK 預覽版本，請傳送電子郵件至 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com)。

讓我們開始吧！

## <a name="prerequisites"></a>必要條件
請確定您具有下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。 
    * 或者，您可以使用 [Azure DocumentDB 模擬器](documentdb-nosql-local-emulator.md)來進行本教學課程。
* [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) 和 [.NET Core 1.0.1 - VS 2015 Tooling Preview 2](https://go.microsoft.com/fwlink/?LinkID=827546)
    * 如果您正在操作 MacOS 或 Linux，可以針對選定平台安裝 [.NET Core SDK](https://www.microsoft.com/net/core#macos)，以從命令列開發 .NET Core 應用程式。 
    * 如果您正在操作 Windows，可以安裝 [.NET Core SDK](https://www.microsoft.com/net/core#windows) 以從命令列開發 .NET Core 應用程式。 
    * 您可以使用自己的編輯器，也可以下載適用於 Windows、Linux 和 MacOS 的免費 [Visual Studio Code](https://code.visualstudio.com/)。 

## <a name="step-1-create-a-documentdb-account"></a>步驟 1：建立 DocumentDB 帳戶
讓我們建立 DocumentDB 帳戶。 如果您已經擁有想要使用的帳戶，就可以跳到 [設定您的 Visual Studio 方案](#SetupVS)。 如果您使用 DocumentDB 模擬器，請遵循 [Azure DocumentDB 模擬器](documentdb-nosql-local-emulator.md)的步驟來設定模擬器，並請直接跳到[安裝 Visual Studio 方案](#SetupVS)。

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupvsastep-2-setup-your-visual-studio-solution"></a><a id="SetupVS"></a>步驟 2：設定您的 Visual Studio 方案
1. 在電腦上開啟 **Visual Studio 2015** 。
2. 從 [檔案] 功能表中，選取 [新增]，然後選擇 [專案]。
3. 在 [新增專案] 對話方塊中，依序選取 [範本] / [Visual C#] / [.NET Core]/[主控台應用程式 (.NET Core)]、為專案命名，然後按一下 [確定]。
   ![[新增專案] 視窗的螢幕擷取畫面](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. 在 [方案總管] 中，以滑鼠右鍵按一下 Visual Studio 方案底下的新主控台應用程式。
5. 然後不需離開功能表，按一下 [管理 NuGet 套件...]****
   ![專案右鍵點選功能表的螢幕擷取畫面](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. 在 [Nuget] 索引標籤中按一下 [瀏覽]，然後在搜尋方塊中輸入 **azure documentdb**。
7. 在結果中尋找 [Microsoft.Azure.DocumentDB.Core]，然後按一下 [安裝]。
   DocumentDB 用戶端程式庫的封裝識別碼為 [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)

太棒了！ 現在已完成安裝程式，讓我們開始撰寫一些程式碼。 您可以在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)找到本教學課程的完整程式碼專案。

## <a name="a-idconnectastep-3-connect-to-a-documentdb-account"></a><a id="Connect"></a>步驟 3：連接到 DocumentDB 帳戶
首先，在 Program.cs 檔案中，將這些參考新增到 C# 應用程式的開頭：

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> 若要完成此 NoSQL 教學課程，請務必加入上述的相依性。
> 
> 

現在，在「public class Program」之下加入下列兩個常數和您的「client」變數。

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUri = "<your endpoint URI>";
        private const string PrimaryKey = "<your key>";
        private DocumentClient client;

接下來，前往 [Azure 入口網站](https://portal.azure.com) 擷取您的 URI 和主索引鍵。 需要 DocumentDB URI 和主索引鍵，您的應用程式才能了解要在連接到哪裡，而 DocumentDB 才會信任您的應用程式連接。

在 Azure 入口網站中，瀏覽至 DocumentDB 帳戶，然後按一下 [金鑰] 。

從入口網站複製 URI，並將它貼到 program.cs 檔案的 `<your endpoint URI>` 中。 然後從入口網站複製主要金鑰，並將它貼到 `<your key>`中。 如果您使用 Azure DocumentDB 模擬器，請將 `https://localhost:443` 做為端點，並使用[如何使用 DocumentDB 模擬器開發](documentdb-nosql-local-emulator.md)中的定義完善授權金鑰。

![NoSQL 教學課程用來建立 C# 主控台應用程式之 Azure 入口網站的螢幕擷取畫面。 顯示 DocumentDB 帳戶，內含反白顯示的 [主動式] 集線器、[DocumentDB 帳戶] 刀鋒視窗上反白顯示的 [金鑰] 按鈕、[金鑰] 刀鋒視窗上反白顯示的 [URI]、[主要金鑰] 和 [次要金鑰] 值][keys]

讓我們建立 **DocumentClient**的新執行個體，啟動快速入門應用程式。

在 **Main** 方法底下，加入 **GetStartedDemo** 這個新的非同步工作，以將新的 **DocumentClient** 具現化。

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
    }

加入下列程式碼，以從 **Main** 方法執行非同步工作。 **Main** 方法會攔截例外狀況並將它們寫入主控台。

    static void Main(string[] args)
    {
            // ADD THIS PART TO YOUR CODE
            try
            {
                    Program p = new Program();
                    p.GetStartedDemo().Wait();
            }
            catch (DocumentClientException de)
            {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
            }
            catch (Exception e)
            {
                    Exception baseException = e.GetBaseException();
                    Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
            }
            finally
            {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
            }

按 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功連接到 DocumentDB 帳戶，讓我們立即看看如何使用 DocumentDB 資源。  

## <a name="step-4-create-a-database"></a>步驟 4：建立資料庫
在您新增用於建立資料庫的程式碼之前，請新增用於寫入主控台的協助程式方法。

複製 **WriteToConsoleAndPromptToContinue** 方法並貼到 **GetStartedDemo** 方法之下。

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

可以使用 **DocumentClient** 類別的 [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 方法建立 DocumentDB [資料庫](documentdb-resources.md#databases)。 資料庫是分割給多個集合之 JSON 文件儲存體的邏輯容器。

複製下列程式碼並貼到 **GetStartedDemo** 方法的用戶端建立之下。 這會建立名為 FamilyDB 的資料庫。

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

按 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功建立 DocumentDB 資料庫。  

## <a name="a-idcreatecollastep-5-create-a-collection"></a><a id="CreateColl"></a>步驟 5：建立集合
> [!WARNING]
> **CreateDocumentCollectionAsync** 會建立含有保留輸送量且具有價格含意的新集合。 如需詳細資訊，請造訪 [定價頁面](https://azure.microsoft.com/pricing/details/documentdb/)。
> 
> 

您可以使用 **DocumentClient** 類別的 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 方法建立[集合](documentdb-resources.md#collections)。 集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。

複製下列程式碼並貼到 **GetStartedDemo** 方法的資料庫建立之下。 這會建立名為 FamilyCollection_oa 的文件集合。

        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

        // ADD THIS PART TO YOUR CODE
        await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

按 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功建立 DocumentDB 文件集合。  

## <a name="a-idcreatedocastep-6-create-json-documents"></a><a id="CreateDoc"></a>步驟 6：建立 JSON 文件
您可以使用 **DocumentClient** 類別的 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 方法來建立[文件](documentdb-resources.md#documents)。 文件會是使用者定義的 (任意) JSON 內容。 現在可插入一或多份文件。 如果您已經有想要儲存於資料庫中的資料，就可以使用 DocumentDB 的 [資料移轉工具](documentdb-import-data.md)。

首先，我們需要建立 **Family** 類別以代表此範例中儲存在 DocumentDB 內的物件。 我們也會建立 **Family** 內使用的 **Parent**、**Child**、**Pet**、**Address** 子類別。 請注意，文件必須將 **Id** 屬性序列化為 JSON 中的**識別碼**。 藉由在 **GetStartedDemo** 方法之後加入下列內部子類別來建立這些類別。

複製 **Family**、**Parent**、**Child**、**Pet** 和 **Address** 類別並貼到 **WriteToConsoleAndPromptToContinue** 方法之下。

    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }

    // ADD THIS PART TO YOUR CODE
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
                return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

複製 **CreateFamilyDocumentIfNotExists** 方法並貼到 **CreateDocumentCollectionIfNotExists** 方法之下。

    // ADD THIS PART TO YOUR CODE
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
            }
            else
            {
                throw;
            }
        }
    }

插入兩個文件，一個給 Andersen 家族，另一個給 Wakefield 家族。

複製下列程式碼並貼到 **GetStartedDemo** 方法的文件集合建立之下。

    await this.CreateDatabaseIfNotExists("FamilyDB_oa");

    await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO YOUR CODE
    Family andersenFamily = new Family
    {
            Id = "Andersen.1",
            LastName = "Andersen",
            Parents = new Parent[]
            {
                    new Parent { FirstName = "Thomas" },
                    new Parent { FirstName = "Mary Kay" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FirstName = "Henriette Thaulow",
                            Gender = "female",
                            Grade = 5,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Fluffy" }
                            }
                    }
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

    Family wakefieldFamily = new Family
    {
            Id = "Wakefield.7",
            LastName = "Wakefield",
            Parents = new Parent[]
            {
                    new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                    new Parent { FamilyName = "Miller", FirstName = "Ben" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FamilyName = "Merriam",
                            FirstName = "Jesse",
                            Gender = "female",
                            Grade = 8,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Goofy" },
                                    new Pet { GivenName = "Shadow" }
                            }
                    },
                    new Child
                    {
                            FamilyName = "Miller",
                            FirstName = "Lisa",
                            Gender = "female",
                            Grade = 1
                    }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

按 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功建立兩個 DocumentDB 文件。  

![說明 NoSQL 教學課程用來建立 C# 主控台應用程式之帳戶、線上資料庫、集合和文件之間階層式關聯性的圖表](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a name="a-idqueryastep-7-query-documentdb-resources"></a><a id="Query"></a>步驟 7：查詢 DocumentDB 資源
DocumentDB 支援對儲存於每個集合的 JSON 文件進行豐富 [查詢](documentdb-sql-query.md) 。  下列範例程式碼示範可在我們於前一個步驟中插入的文件上執行的各種查詢 (同時使用 DocumentDB SQL 語法和 LINQ)。

複製 **ExecuteSimpleQuery** 方法並貼到 **CreateFamilyDocumentIfNotExists** 方法之下。

    // ADD THIS PART TO YOUR CODE
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

            // Here we find the Andersen family via its LastName
            IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                    .Where(f => f.LastName == "Andersen");

            // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
            Console.WriteLine("Running LINQ query...");
            foreach (Family family in familyQuery)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            // Now execute the same query via direct SQL
            IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                    "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                    queryOptions);

            Console.WriteLine("Running direct SQL query...");
            foreach (Family family in familyQueryInSql)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

複製下列程式碼並貼到 **GetStartedDemo** 方法的次要文件建立之下。

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

按 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功查詢 DocumentDB 集合。

下圖說明如何針對您所建立的集合呼叫 DocumentDB SQL 查詢語法，相同邏輯也可以套用至 LINQ 查詢。

![說明 NoSQL 教學課程用來建立 C# 主控台應用程式之查詢的範圍和意義的圖表](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

因為 DocumentDB 查詢已侷限於單一集合，所以查詢中的 [FROM](documentdb-sql-query.md#from-clause) 關鍵字是選擇性的。 因此，"FROM Families f" 可以換成 "FROM root r"，或您選擇的任何其他變數名稱。 依預設，DocumentDB 會推斷該系列、根或您選擇的變數名稱來參考目前的集合。

## <a name="a-idreplacedocumentastep-8-replace-json-document"></a><a id="ReplaceDocument"></a>步驟 8︰取代 JSON 文件
DocumentDB 支援取代 JSON 文件。  

複製 **ReplaceFamilyDocument** 方法並貼到 **ExecuteSimpleQuery** 方法之下。

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
        try
        {
            await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
            this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

複製下列程式碼並貼到 **GetStartedDemo** 方法的查詢執行之下。 取代文件後，此程式碼會再次執行相同的查詢以檢視變更後的文件。

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

按 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功取代 DocumentDB 文件。

## <a name="a-iddeletedocumentastep-9-delete-json-document"></a><a id="DeleteDocument"></a>步驟 9︰刪除 JSON 文件
DocumentDB 支援刪除 JSON 文件。  

複製 **DeleteFamilyDocument** 方法並貼到 **ReplaceFamilyDocument** 方法之下。

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        try
        {
            await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
            Console.WriteLine("Deleted Family {0}", documentName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

複製下列程式碼並貼到 **GetStartedDemo** 方法的次要查詢執行之下。

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

按 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功刪除 DocumentDB 文件。

## <a name="a-iddeletedatabaseastep-10-delete-the-database"></a><a id="DeleteDatabase"></a>步驟 10：刪除資料庫
刪除已建立的資料庫將會移除資料庫和所有子系資源 (集合、文件等)。

複製下列程式碼並貼到 **GetStartedDemo** 方法的文件刪除之下，以刪除整個資料庫和所有子系資源。

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));

按 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功刪除 DocumentDB 資料庫。

## <a name="a-idrunastep-11-run-your-c-console-application-all-together"></a><a id="Run"></a>步驟 11：一起執行您的 C# 主控台應用程式！
在 Visual Studio 中按 F5，即可在偵錯模式下建置應用程式。

您應該可以看到入門應用程式的輸出。 輸出將會顯示新增的查詢結果，而且應該符合以下的範例文字。

    Created FamilyDB_oa
    Press any key to continue ...
    Created FamilyCollection_oa
    Press any key to continue ...
    Created Family Andersen.1
    Press any key to continue ...
    Created Family Wakefield.7
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Replaced Family Andersen.1
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Deleted Family Andersen.1
    End of demo, press any key to exit.

恭喜！ 您已經完成本 NoSQL 教學課程，並擁有運作中的 C# 主控台應用程式！

## <a name="a-idgetsolutiona-get-the-complete-nosql-tutorial-solution"></a><a id="GetSolution"></a> 取得完整的 NoSQL 教學課程方案
若要建置包含本文中所有範例的 GetStarted 方案，您將需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。
* [DocumentDB 帳戶][documentdb-create-account]。
* 您可以在 GitHub 上找到 [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) 方案。

若要在 Visual Studio 中還原 DocumentDB .NET Core SDK 的參考，請在 [方案總管] 的 **GetStarted** 方案上按一下滑鼠右鍵，然後按一下 [啟用 NuGet 封裝還原]。 接下來，在 Program.cs 檔案中更新 EndpointUrl 和 AuthorizationKey 值，如 [連接至 DocumentDB 帳戶](#Connect)中所述。

## <a name="next-steps"></a>後續步驟
* 需要更複雜的 ASP.NET MVC NoSQL 教學課程嗎？ 請參閱 [使用 DocumentDB 建置具有 ASP.NET MVC 的 Web 應用程式](documentdb-dotnet-application.md)。
* 想要執行 DocumentDB 的規模和效能測試？ 請參閱 [Azure DocumentDB 的效能和級別測試](documentdb-performance-testing.md)
* 了解如何 [監視 DocumentDB 帳戶](documentdb-monitor-accounts.md)(英文)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中，針對範例資料集執行查詢。
* 如需深入了解程式設計模型，請參閱 [DocumentDB 文件頁面](https://azure.microsoft.com/documentation/services/documentdb/)中的＜開發＞一節。

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png



<!--HONumber=Jan17_HO2-->


