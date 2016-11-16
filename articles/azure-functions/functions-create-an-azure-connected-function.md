---
title: "建立繫結至 Azure 服務的 Azure 函式 | Microsoft Docs"
description: "建置 Azure 函式 (無伺服器的應用程式)，以與其他 Azure 服務互動。"
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>建立繫結至 Azure 服務的 Azure 函式
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

在這個簡短影片中，您將了解如何建立 Azure 函式，以接聽 Azure 佇列上的訊息以及將訊息複製至 Azure Blob。

## <a name="watch-the-video"></a>觀賞影片
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>建立輸入佇列觸發程序函式
此函式的目標是每隔 10 秒將訊息寫入至佇列。 為了達到此目標，您必須建立函式和訊息佇列，並新增程式碼以將訊息寫入新建立的佇列。

1. 移至 Azure 入口網站，並找出您的 Azure 函式應用程式。
2. 按一下 [新增函式] > [TimerTrigger - Node]。 將函式命名為 **FunctionsBindingsDemo1**
3. 輸入「0/10     *」的值做為排程。 此值是 cron 運算式的形式。 這會將計時器排程為每隔 10 秒執行一次。
4. 按一下 [建立] 按鈕以建立函式。
   
    ![新增觸發程序計時器函式](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. 檢視記錄檔中的活動，確認函式可運作。 您可能必須按一下右上角的 [記錄檔] 連結以顯示 [記錄檔] 窗格。
   
   ![檢視記錄檔以確認函式可運作](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>新增訊息佇列
1. 移至 [整合] 索引標籤。
2. 選擇 [新增輸出] > [Azure 儲存體佇列] > [選取]。
3. 在 [訊息參數名稱] 文字方塊中輸入 **myQueueItem**。
4. 選取儲存體帳戶，或按一下 [新增] 來建立儲存體帳戶 (如果您沒有現有帳戶)。
5. 在 [佇列名稱] 文字方塊中輸入 **functions-bindings**。
6. 按一下 [儲存] 。  
   
   ![新增觸發程序計時器函式](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>寫入至訊息佇列
1. 返回 [開發] 索引標籤，然後在函式的現有程式碼之後新增下列程式碼︰
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. 修改現有函式程式碼，以呼叫步驟 1 中新增的程式碼。 在函式第 9 行附近的「if」陳述式之後插入下列程式碼。
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    此程式碼會建立 **myQueueItem**，並將其 [時間] 屬性設定為目前的 timeStamp。 然後，它會將新的佇列項目新增至內容的 myQueue 繫結。
3. 按一下 [儲存並執行]。
4. 在 Visual Studio 中檢視佇列，確認程式碼可以運作。
   
   * 開啟 Visual Studio，然後移至 [檢視] > [雲端總管]。
   * 尋找您在建立 myQueue 佇列時所使用的儲存體帳戶和 **functions-bindings** 佇列。 您應該會看到幾列記錄檔資料。 您可能必須透過 Visual Studio 登入 Azure。  

## <a name="create-an-output-queue-trigger-function"></a>建立輸出佇列觸發程序函式
1. 按一下 [新增函式] > [QueueTrigger - C#]。 將函式命名為 **FunctionsBindingsDemo2**。 請注意，您可以在相同函式應用程式中混用語言 (在此案例中為 Node 和 C#)。
2. 在 [佇列名稱] 欄位中輸入 **functions-bindings**。
3. 選取要使用的儲存體帳戶，或建立新帳戶。
4. 按一下 [建立] 
5. 檢視函式記錄檔和 Visual Studio 中的更新，確認新函式可以運作。 函式記錄檔會顯示函式正在執行，而且項目已從佇列中清除。 由於函式是以輸入觸發程序的形式繫結至 **functions-bindings** 輸出佇列，在 Visual Studio 中重新整理 **functions-bindings** 佇列應該會顯現出項目已消失。 它們已從佇列中清除。   
   
   ![新增輸出佇列計時器函式](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>將佇列項目類型從 JSON 修改為物件
1. 以下列程式碼取代 **FunctionsBindingsDemo2** 中的程式碼︰    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    此程式碼會新增 **TableItem** 和 **QItem** 兩個類別，供您用來讀取和寫入至佇列。 此外，**Run** 函式已修改為接受 **QItem** 和 **TraceWriter** 參數，而不是 **string** 和 **TraceWriter**。 
2. 按一下 [儲存]  按鈕。
3. 檢查記錄檔，確認程式碼可以運作。 請注意，Azure 函式會自動將物件序列化和還原序列化，讓您以物件導向方式輕鬆地存取佇列，以傳遞資料。 

## <a name="store-messages-in-an-azure-table"></a>將訊息儲存在 Azure 資料表
您已讓佇列一起運作，現在是時候新增 Azure 資料表以永久儲存佇列資料。

1. 移至 [整合] 索引標籤。
2. 為輸出建立 Azure 儲存體資料表，並將它命名為 **myTable**。
3. 針對「應該將資料寫入哪一個資料表？」問題，回答 **functionsbindings**。
4. 將 **PartitionKey** 設定從 **{project-id}** 變更為 **{partition}**。
5. 選擇儲存體帳戶，或建立新帳戶。
6. 按一下 [儲存] 。
7. 移至 [開發] 索引標籤。
8. 建立 **TableItem** 類別來代表 Azure 資料表，並修改 Run 函式以接受新建立的 TableItem 物件。 請注意，您必須使用 **PartitionKey** 和 **RowKey** 屬性，函式才能正常運作。
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. 按一下 [儲存] 。
10. 檢視函式記錄檔以及在 Visual Studio 中，確認程式碼可運作。 若要在 Visual Studio 中確認，請使用 [雲端總管] 瀏覽至 **functionbindings** Azure 資料表，並確認其中有資料列。

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


