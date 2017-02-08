---
title: "開始使用 Azure 佇列儲存體和 Visual Studio 已連線的服務 (ASP.NET) | Microsoft Docs"
description: "在使用 Visual Studio 已連接服務連接到儲存體帳戶之後，如何在 Visual Studio ASP.NET 專案中開始使用 Azure 佇列儲存體"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f58c2b522f81dc2dc86f0d2c6bc4872504cf7377
ms.openlocfilehash: 70875287e79aaf49e1b8802cf2953cd5381b97f4


---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>開始使用 Azure 佇列儲存體和 Visual Studio 已連線的服務 (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

Azure 佇列儲存體是一項儲存大量非結構化資料的服務，而這些資料可透過 HTTP 或 HTTPS 存取。 單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含不限數量的訊息，以儲存體帳戶的總容量為限。

本文說明如何以程式設計方式管理 Azure 佇列儲存體實體並執行常見工作，例如建立 Azure 佇列，以及新增、修改、讀取和移除佇列訊息。

> [!NOTE]
> 
> 本文中的程式碼區段假設您已使用已連線的服務，連接到 Azure 儲存體帳戶。 開啟 [Visual Studio 方案總管]，以滑鼠右鍵按一下專案，並從內容功能表中選取 [新增] -> [已連線的服務] 選項，即可設定已連線的服務。 從該處，遵循對話方塊的指示，以連接到所需的 Azure 儲存體帳戶。      

## <a name="create-a-queue"></a>建立佇列

下列步驟說明如何以程式設計方式建立佇列。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。

1. 新增下列 using 指示詞︰
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得代表佇列服務用戶端的 **CloudQueueClient** 物件。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得代表所需佇列名稱參考的 **CloudQueue** 物件。 (將 <queue-name> 變更為您想要建立的佇列名稱。)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. 呼叫 **CloudQueue.CreateIfNotExists** 方法來建立佇列 (如果尚不存在)。 

        queue.CreateIfNotExists();


## <a name="add-a-message-to-a-queue"></a>將訊息新增至佇列

下列步驟說明如何以程式設計方式將訊息新增至佇列。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。

1. 新增下列 using 指示詞︰
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得代表佇列服務用戶端的 **CloudQueueClient** 物件。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得代表所需佇列名稱參考的 **CloudQueue** 物件。 (將 <queue-name> 變更為您想要新增訊息的佇列名稱。)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. 建立 **CloudQueueMessage** 物件，代表您想要新增至佇列的訊息。 您可以從字串 (採用 UTF-8 格式) 或位元組陣列建立 **CloudQueueMessage** 物件。 (將 <queue-message> 變更為您想要新增的訊息。

        CloudQueueMessage message = new CloudQueueMessage(<queue-message>);

6. 呼叫 **CloudQueue.AddMessage** 方法，以將訊息新增至佇列。

        queue.AddMessage(message);

## <a name="read-a-message-from-a-queue-without-removing-it"></a>讀取佇列中的訊息，但不移除它

下列步驟說明如何以程式設計方式檢視已排入佇列的訊息 (讀取第一則訊息，但不移除它)。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。 

1. 新增下列 using 指示詞︰
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得代表佇列服務用戶端的 **CloudQueueClient** 物件。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得 **CloudQueue** 物件，代表佇列的參考。 (將 <queue-name> 變更為您想要讀取其訊息的佇列名稱。)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. 呼叫 **CloudQueue.PeekMessage** 方法，以讀取佇列中的第一則訊息，無需將它從佇列中移除。

        CloudQueueMessage message = queue.PeekMessage();

6. 使用 **CloudQueueMessage.AsBytes** 或 **CloudQueueMessage.AsString** 屬性，存取 **CloudQueueMessage** 物件的值。

        string messageAsString = message.AsString;
        byte[] messageAsBytes = message.AsBytes;

## <a name="read-and-remove-a-message-from-a-queue"></a>讀取並移除佇列中的訊息

下列步驟說明如何以程式設計方式讀取已排入佇列的訊息，然後將它刪除。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。 

1. 新增下列 using 指示詞︰
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得代表佇列服務用戶端的 **CloudQueueClient** 物件。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得 **CloudQueue** 物件，代表佇列的參考。 (將 <queue-name> 變更為您想要讀取其訊息的佇列名稱。)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. 呼叫 **CloudQueue.AddMessage** 方法，以讀取佇列中的第一則訊息。 **CloudQueue.GetMessage** 方法可讓讀取訊息的任何其他程式碼看不見此訊息 30 秒 (預設值)，其他程式碼便無法在您處理此訊息時進行修改或刪除。 若要變更看不見訊息的時間量，請修改傳遞至 **CloudQueue.GetMessage** 方法的 **visibilityTimeout** 參數。

        // This message will be invisible to other code for 30 seconds.
        CloudQueueMessage message = queue.GetMessage();     

6. 呼叫 **CloudQueueMessage.Delete** 方法，以刪除佇列中的訊息。

        queue.DeleteMessage(message);

## <a name="get-the-queue-length"></a>取得佇列長度

下列步驟說明如何以程式設計方式取得佇列長度 (訊息數目)。 在 ASP.NET MVC 應用程式中，程式碼會移入控制器中。 

1. 新增下列 using 指示詞︰
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得代表佇列服務用戶端的 **CloudQueueClient** 物件。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得 **CloudQueue** 物件，代表佇列的參考。 (將 <queue-name> 變更為您正在查詢其長度的佇列名稱。)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. 呼叫 **CloudQueue.FetchAttributes** 方法來擷取佇列的屬性 (包括其長度)。 

        queue.FetchAttributes();

6. 存取 **CloudQueue.ApproximateMessageCount** 屬性，以取得佇列的長度。
 
        int? nMessages = queue.ApproximateMessageCount;

## <a name="delete-a-queue"></a>刪除佇列
下列步驟說明如何以程式設計方式刪除佇列。 

1. 新增下列 using 指示詞︰
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 <storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得代表佇列服務用戶端的 **CloudQueueClient** 物件。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得 **CloudQueue** 物件，代表佇列的參考。 (將 <queue-name> 變更為您正在查詢其長度的佇列名稱。)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. 呼叫 **CloudQueueMessage.Delete** 方法，以刪除 **CloudQueue** 物件所代表的佇列。

        messageQueue.Delete();

## <a name="next-steps"></a>後續步驟
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Dec16_HO2-->


