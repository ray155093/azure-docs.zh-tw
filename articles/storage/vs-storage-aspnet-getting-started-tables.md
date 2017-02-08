---
title: "開始使用 Azure 資料表儲存體和 Visual Studio 已連線的服務 (ASP.NET) | Microsoft Docs"
description: "在使用 Visual Studio 已連線的服務連接到儲存體帳戶之後，如何在 Visual Studio ASP.NET 專案中開始使用 Azure 資料表儲存體"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 07f827e13e5f01a373e69b90e8a5a0c72081acd0
ms.openlocfilehash: 24cfb3217dbadba3a086a8b0251efe5ab0173d1f


---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>開始使用 Azure 資料表儲存體和 Visual Studio 已連線的服務 (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀

Azure 資料表儲存體可讓您儲存大量的結構化資料。 此服務是一個 NoSQL 資料存放區，接受來自 Azure 雲端內外經過驗證的呼叫。 Azure 資料表很適合儲存結構化、非關聯式資料。

本文說明如何以程式設計方式管理 Azure 表格儲存體實體，並執行常見工作，例如建立和刪除資料表)，以及使用資料表實體。 

> [!NOTE]
> 
> 本文中的程式碼區段假設您已使用已連線的服務，連接到 Azure 儲存體帳戶。 開啟 [Visual Studio 方案總管]，以滑鼠右鍵按一下專案，並從內容功能表中選取 [新增] -> [已連線的服務] 選項，即可設定已連線的服務。 從該處，遵循對話方塊的指示，以連接到所需的 Azure 儲存體帳戶。      

## <a name="create-a-table-in-code"></a>在程式碼中建立資料表

下列步驟說明如何以程式設計方式建立資料表。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。

1. 新增下列 using 指示詞︰

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得 **CloudTableClient** 物件，代表表格服務用戶端。

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 取得 **CloudTable** 物件，代表所需資料表名稱的參考。 (將 <table-name> 變更為您想要建立的資料表名稱。)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 呼叫 **CloudTable.CreateIfNotExists** 方法來建立資料表 (如果尚不存在)。   
   
        table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>將實體加入至資料表

下列步驟說明如何以程式設計方式將實體新增至資料表。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。 

1. 新增下列 using 指示詞︰

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得 **CloudTableClient** 物件，代表表格服務用戶端。

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 取得 **CloudTable** 物件，代表所需資料表名稱的參考。 (將 <table-name> 變更為您想要新增實體的資料表名稱。)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 若要將實體新增至資料表，您可定義衍生自 **TableEntity** 的類別。 下列程式碼使用客戶名字作為資料列索引鍵、並使用姓氏作為資料分割索引鍵的，定義一個稱為 **CustomerEntity** 的實體類別。

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. 具現化實體。

        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.Email = "Walter@contoso.com";

7. 建立 **TableOperation** 物件，以插入客戶實體。

        TableOperation insertOperation = TableOperation.Insert(customer1);

8. 呼叫 **CloudTable.Execute** 方法以執行插入作業。 您可以檢查 **TableResult.HttpStatusCode** 屬性，以確認作業的結果。 狀態碼 2xx 表示已成功處理用戶端所要求的動作。 例如，成功插入新實體會造成 HTTP 狀態碼 204，這表示已成功處理作業且伺服器未傳回任何內容。

        TableResult result = table.Execute(insertOperation);

        // Inspect result.HttpStatusCode for success/failure.

## <a name="add-a-batch-of-entities-to-a-table"></a>將一批實體新增至資料表

除了能夠一次將一個實體新增至一個資料表，您也可以分批新增實體。 這可減少程式碼與 Azure 資料表服務之間的來回行程次數。 下列步驟說明如何使用單一作業以程式設計方式將多個實體新增至資料表。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。

1. 新增下列 using 指示詞︰

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得 **CloudTableClient** 物件，代表表格服務用戶端。

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 取得 **CloudTable** 物件，代表所需資料表名稱的參考。 (將 <table-name> 變更為您想要新增實體的資料表名稱。)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 若要將實體新增至資料表，您可定義衍生自 **TableEntity** 的類別。 下列程式碼使用客戶名字作為資料列索引鍵、並使用姓氏作為資料分割索引鍵的，定義一個稱為 **CustomerEntity** 的實體類別。

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. 具現化實體。

        CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
        customer1.Email = "Jeff@contoso.com";
    
        CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
        customer2.Email = "Ben@contoso.com";

7. 取得 **TableBatchOperation** 物件。

        TableBatchOperation batchOperation = new TableBatchOperation();

8. 將實體新增至批次插入作業。

        batchOperation.Insert(customer1);
        batchOperation.Insert(customer2);

9. 呼叫 **CloudTable.ExecuteBatch** 方法以執行批次插入作業。 **CloudTable.ExecuteBatch** 方法會傳回 **TableResult** 物件清單。 您可以檢查清單中每個 **TableResult** 物件的 **TableResult.HttpStatusCode** 屬性，以確認批次插入作業的結果。 狀態碼 2xx 表示已成功處理用戶端所要求的動作。 例如，成功插入新實體會造成 HTTP 狀態碼 204，這表示已成功處理作業且伺服器未傳回任何內容。
    
        IList<TableResult> results = table.ExecuteBatch(batchOperation);

        // Inspect the HttpStatusCode property of each TableResult object
        // in the results list for success/failure.

## <a name="get-a-single-entity"></a>取得單一實體

下列步驟說明如何以程式設計方式從資料表取得實體。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。 

> [!NOTE]
> 
> 本節中的程式碼會參考[將一批實體新增至資料表](#add-a-batch-of-entities-to-a-table)一節中出現的 **CustomerEntity** 類別和資料。 

1. 新增下列 using 指示詞︰

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得 **CloudTableClient** 物件，代表表格服務用戶端。

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 取得 **CloudTable** 物件，代表所需資料表名稱的參考。 (將 <table-name> 變更為您想要新增實體的資料表名稱。)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 建立擷取作業物件，其採用衍生自 **TableEntity** 的實體物件。 第一個參數是 partitionKey，而第二個參數是 rowKey。 使用[將一批實體新增至資料表](#add-a-batch-of-entities-to-a-table)一節中顯示的 **CustomerEntity** 類別和資料，下列程式碼片段可查詢資料表中是否有 *partitionKey* 值為 "Smith" 且 rowKey 值為 "Ben"的 **CustomerEntity** 實體。  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. 執行擷取作業。   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. 檢查 **TableOperation.HttpStatusCode** 屬性以確認作業的結果，其中狀態碼 200 表示已成功處理用戶端所要求的動作。 您也可以檢查 **TableResult.Result** 屬性，如果作業成功，該屬性將包含所傳回的實體。

        CustomerEntity customer = null;

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            // Process the customer entity.
            customer = retrievedResult.Result as CustomerEntity;
        }

## <a name="get-all-entities-in-a-partition"></a>取得資料分割中的所有實體

下列步驟說明如何以程式設計方式從資料分割取得所有實體。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。 

> [!NOTE]
> 
> 本節中的程式碼會參考[將一批實體新增至資料表](#add-a-batch-of-entities-to-a-table)一節中出現的 **CustomerEntity** 類別和資料。 

1. 新增下列 using 指示詞︰

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得 **CloudTableClient** 物件，代表表格服務用戶端。

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 取得 **CloudTable** 物件，代表所需資料表名稱的參考。 (將 <table-name> 變更為您想要新增實體的資料表名稱。)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 具現化 **TableQuery** 物件，以在 **Where** 子句中指定查詢。 使用[將一批實體新增至資料表](#add-a-batch-of-entities-to-a-table)一節中顯示的 **CustomerEntity** 類別和資料，下列程式碼片段可查詢資料表中是否有 **PartitionKey** 值為 "Smith" 的所有實體。

        TableQuery<CustomerEntity> query = 
            new TableQuery<CustomerEntity>()
            .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

6. 在迴圈內呼叫 **CloudTable.ExecuteQuerySegmented** 方法，以傳遞您在上一個步驟中具現化的查詢物件。  **CloudTable.ExecuteQuerySegmented** 方法會傳回 **TableContinuationToken** 物件 - 若為 **null**，表示再也沒有要擷取的實體。 在迴圈內，使用另一個迴圈來逐一查看所傳回的實體。

        TableContinuationToken token = null;
        do
        {
            TableQuerySegment<CustomerEntity>resultSegment = table.ExecuteQuerySegmented(query, token);
            token = resultSegment.ContinuationToken;

            foreach (CustomerEntity customer in resultSegment.Results)
            {
                // Process customer entity.
            }
        } while (token != null);

## <a name="delete-an-entity"></a>刪除實體

下列步驟說明如何搜尋而後刪除實體。

1. 新增下列 using 指示詞︰

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得 **CloudTableClient** 物件，代表表格服務用戶端。

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. 取得 **CloudTable** 物件，代表所需資料表名稱的參考。 (將 <table-name> 變更為您想要新增實體的資料表名稱。)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. 建立擷取作業物件，其採用衍生自 **TableEntity** 的實體物件。 第一個參數是 partitionKey，而第二個參數是 rowKey。 使用[將一批實體新增至資料表](#add-a-batch-of-entities-to-a-table)一節中顯示的 **CustomerEntity** 類別和資料，下列程式碼片段可查詢資料表中是否有 *partitionKey* 值為 "Smith" 且 rowKey 值為 "Ben"的 **CustomerEntity** 實體。  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. 執行擷取作業。   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. 檢查 **TableOperation.HttpStatusCode** 屬性以確認作業的結果，其中狀態碼 200 表示已成功處理用戶端所要求的動作。 您也可以檢查 **TableResult.Result** 屬性，如果作業成功，該屬性將包含所傳回的實體。 在條件式陳述式內，若要確認作業成功，請建立刪除作業 (從查詢傳遞所傳回的實體)，然後執行刪除作業。

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            CustomerEntity customer = retrievedResult.Result as CustomerEntity;

            // Create the delete operation.
            TableOperation deleteOperation = TableOperation.Delete(customer);

            // Execute the delete operation.
            table.Execute(deleteOperation);
        }

## <a name="next-steps"></a>後續步驟
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]




<!--HONumber=Dec16_HO2-->


