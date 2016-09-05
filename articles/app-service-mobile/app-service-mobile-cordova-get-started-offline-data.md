<properties
    pageTitle="啟用 Azure 行動應用程式的離線同步處理 (Cordova) | Microsoft Azure"
    description="了解如何在 Cordova 應用程式中使用 App Service 行動應用程式快取和同步離線資料"
    documentationCenter="cordova"
    authors="mikejo5000"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="08/14/2016"
    ms.author="mikejo"/>

# 啟用 Cordova 行動應用程式的離線同步處理

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

此教學課程介紹適用於 Cordova 之 Azure 行動應用程式的離線同步處理功能。離線同步處理可讓使用者與行動應用程式進行互動 - 檢視、新增或修改資料 - 即使沒有網路連線也可行。變更會儲存在本機資料庫中︰裝置上線後，這些變更就會與遠端服務進行同步處理。

本教學課程是根據您完成教學課程 [Apache Cordova 快速入門]時所建立之 Mobile Apps 的 Cordova 快速入門方案。在本教學課程中，您將會更新快速入門方案來新增 Azure Mobile Apps 的離線功能。我們也會在應用程式中反白顯示離線特有的程式碼。

若要深入了解離線同步處理功能，請參閱 [Azure Mobile Apps 中的離線資料同步處理]主題。如需 API 使用方式的詳細資訊，請參閱外掛程式中的[讀我檔案]。

## 在快速入門方案中新增離線同步處理

應用程式中必須新增離線同步處理程式碼。離線同步處理需要 cordova-sqlite-storage 外掛程式，此外掛程式會在專案中納入 Azure Mobile Apps 外掛程式時自動新增至應用程式。快速入門專案包含上述兩個外掛程式。

1. 在 Visual Studio 的 [方案總管] 中開啟 index.js，並將下列程式碼

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    取代為此程式碼：

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. 接下來，將下列程式碼：

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

	取代為此程式碼：

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    前面新增的程式碼會初始化本機存放區，並定義與 Azure 後端所使用的資料行值相符的本機資料表 (您不需要在此程式碼中包含所有資料行值)。

    您可以藉由呼叫 **getSyncContext** 取得同步處理內容的參考。當呼叫 **push** 時，透過追蹤和推送所有用戶端應用程式修改之資料表中的變更，同步處理內容可協助保存資料表關聯性。

3. 將應用程式的 URL 更新為行動應用程式的應用程式 URL。

4. 接下來，將此程式碼：

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    取代為此程式碼：

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    上述程式碼會初始化同步處理內容，然後呼叫 getSyncTable (而不是 getTable) 來取得本機資料表的參考。

    此程式碼使用本機資料庫來進行所有建立、讀取、更新和刪除 (CRUD) 資料表作業。

    這個範例會對同步處理衝突執行簡單的錯誤處理。實際的應用程式會處理各種錯誤，例如網路狀況、伺服器衝突及其他問題。如需程式碼範例，請參閱[離線同步處理範例]。

5. 接著，新增此函式以執行實際的同步處理。

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    您可以藉由在用戶端所使用的 **syncContext** 物件上呼叫 **push**，決定何時將變更推送至行動應用程式後端。例如，您可以在應用程式中將 **syncBackend** 的呼叫新增至按鈕事件處理常式 (例如新的 [同步處理] 按鈕)，或者您可以新增 **addItemHandler** 函式的呼叫，以在每當新增項目時進行同步處理。

##離線同步處理考量

在範例中，**syncContext** 的 **push** 方法只會在應用程式啟動時於登入的回呼函式中加以呼叫。在實際的應用程式中，您也可以透過手動方式或在網路狀態變更時觸發此同步處理功能。

針對具有內容追蹤之擱置中本機更新的資料表執行提取，該提取作業將自動觸發先前的內容推送。在此範例中重新整理、新增和完成項目時，您可以省略明確的 **push** 呼叫，因為它可能是多餘的 (請先檢查[讀我檔案]以取得最新的功能狀態)。

在提供的程式碼中，遠端 todoItem 資料表中的所有記錄都會進行查詢，但是也可能透過將查詢識別碼與查詢傳遞至 **push** 來篩選記錄。如需詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理]中的＜增量同步處理＞一節。

## (選擇性) 停用驗證

如果尚未設定驗證，而且不想先設定驗證再測試離線同步處理，請將登入的回呼函式註解化，但讓回呼函式內部的程式碼保持取消註解狀態。

將登入程式碼行註解化之後，程式碼看起來應該像下面這樣。

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

現在，當您執行應用程式時 (而非在登入時)，應用程式會與 Azure 後端進行同步處理。

## 執行用戶端應用程式

離線同步現在已啟用，您現在可以在每個平台執行用戶端應用程式至少一次，以填入本機存放區資料庫。稍後，您將會模擬一個離線案例，並在應用程式離線時修改本機存放區中的資料。

## (選擇性) 測試同步處理行為

在本節中，您將透過使用無效的後端應用程式 URL，修改用戶端專案來模擬離線案例。當您新增或變更資料項目時，這些變更會存放在本機存放區，但不會同步處理到後端資料存放區，直到重新建立連線為止。

1. 在 [方案總管] 中開啟 index.js 專案檔案，然後將應用程式的 URL 變更為指向無效的 URL，如下所示︰

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. 在 index.html 中，以相同的無效 URL 更新 CSP `<meta>` 元素。

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. 建置並執行用戶端應用程式，並請注意，當應用程式嘗試在登入之後與後端進行同步處理時，主控台會記錄例外狀況。任何新增項目在可推送至行動後端之前，都只會存留在本機存放區中。用戶端應用程式的行為，會如同它已連接到支援所有建立、讀取、更新、刪除 (CRUD) 作業的後端。

4. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

5. (選擇性) 使用 Visual Studio 檢視您的 Azure SQL Database 資料表，以查看後端資料庫中的資料並無變更。

	在 Visual Studio 中，開啟 [伺服器總管]。瀏覽至 **Azure**->**SQL Database** 中您的資料庫。在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟]。現在您可以瀏覽至您的 SQL Database 資料表和其內容。


## (選擇性) 測試與行動後端的重新連線

在本節中，您會將應用程式重新連接到行動後端，而模擬回到線上狀態的應用程式。當您登入時，資料會同步處理至行動後端。

1. 重新開啟 index.js，並將應用程式的 URL 修正為指向正確 URL。

2. 重新開啟 index.html，並修正 CSP `<meta>` 元素中的應用程式 URL。

3. 重建並執行用戶端應用程式。應用程式在登入後會嘗試與行動應用程式後端同步處理。請確認偵錯主控台沒有記錄任何例外狀況。

4. (選擇性) 使用 SQL Server 物件總管或 REST 工具 (例如 Fiddler) 檢視已更新的資料。請注意，後端資料庫與本機存放區之間尚未同步處理資料。

    請注意，資料庫與本機存放區之間的資料已同步處理，並包含應用程式中斷連接時您所新增的項目。

## 其他資源

* [Azure Mobile Apps 中的離線資料同步處理]

* [雲端報導︰Azure Mobile Services 中的離線同步處理] \(注意︰影片位於 Mobile Services 上，但離線同步處理的運作方式類似在 Azure Mobile Apps 中的方式)

* [Visual Studio Tools for Apache Cordova]

## 後續步驟

* 查看[離線同步處理範例]中更進階的離線同步處理功能，例如衝突解決
* 查看[讀我檔案]中的離線同步處理 API 參考

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova 快速入門]: app-service-mobile-cordova-get-started.md
[讀我檔案]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[離線同步處理範例]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Azure Mobile Apps 中的離線資料同步處理]: app-service-mobile-offline-data-sync.md
[雲端報導︰Azure Mobile Services 中的離線同步處理]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/zh-TW/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0824_2016-->