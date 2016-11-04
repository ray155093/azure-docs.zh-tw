---
title: 使用 Mobile Apps 啟用通用 Windows 平台 (UWP) 應用程式的離線同步處理 | Microsoft Docs
description: 了解如何使用 Azure 行動應用程式快取及同步處理通用 Windows 平台 (UWP) 應用程式中的離線資料。
documentationcenter: windows
author: wesmc7777
manager: dwrede
editor: ''
services: app-service\mobile

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: wesmc

---
# 啟用 Windows 應用程式離線同步處理
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## 概觀
本教學課程說明如何使用 Azure 行動應用程式後端在通用 Windows 平台 (UWP) 應用程式中新增離線支援。離線同步處理可讓使用者與行動應用程式進行互動 - 檢視、新增或修改資料 - 即使沒有網路連線也可行。變更會儲存在本機資料庫中︰裝置上線後，這些變更就會與遠端後端進行同步處理。

在本教學課程中，您將會更新[建立 Windows 應用程式]教學課程中的 UWP 應用程式專案，以支援 Azure Mobile Apps 的離線功能。如果您不要使用下載的快速入門伺服器專案，必須將資料存取擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要深入了解離線同步處理功能，請參閱 [Azure Mobile Apps 中的離線資料同步處理]主題。

## 需求
本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 完成[建立 Windows 應用程式][create a windows app]。
* [Azure 行動服務 SQLite Store][sqlite store nuget]
* [適用於通用 Windows 平台開發的 SQLite](http://www.sqlite.org/downloads)。

## 更新用戶端應用程式以支援離線功能
Azure 行動應用程式的離線功能可讓您在離線狀態時，仍可與本機資料庫互動。若要在您的應用程式中使用這些功能，您必須將 [SyncContext][synccontext] 初始化至本機存放區。接著，請透過 [IMobileServiceSyncTable][IMobileServiceSyncTable] 介面參考您的資料表。SQLite 可做為裝置上的本機存放區。

1. 安裝[適用於通用 Windows 平台的 SQLite 執行階段](http://sqlite.org/2016/sqlite-uwp-3120200.vsix)。
2. 在 Visual Studio 中，開啟您在[建立 Windows 應用程式]教學課程中完成的 UWP 應用程式專案的 NuGet 封裝管理員，然後搜尋並安裝 **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 封裝。
3. 在 [方案總管] 中，以滑鼠右鍵按一下 [參考] > [加入參考...] > [通用 Windows] > [擴充功能]，然後啟用 [適用於通用 Windows 平台的 SQLite] 和 [適用於通用 Windows 平台應用程式的 Visual C++ 2015 執行階段]。
   
    ![新增 SQLite UWP 參考][1]
4. 開啟 MainPage.xaml.cs 檔案，然後取消註解位於檔案開頭處的下列 `using` 陳述式：
   
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  
        using Microsoft.WindowsAzure.MobileServices.Sync;         
5. 針對將 `todoTable` 初始化為 **IMobileServiceTable** 的程式碼行加上註解，然後針對將 `todoTable` 初始化為 **IMobileServiceSyncTable** 的程式碼行取消註解：
   
        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); 
6. 在 MainPage.xaml.cs 的 `Offline sync` 區域中，取消註解下列方法︰
   
        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
   
            await SyncAsync();
        }
   
        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }
   
    在 `SyncAsync` 中，會從 [SyncContext][synccontext] 開始推送作業，然後再進行增量同步處理。同步處理內容會追蹤用戶端針對所有資料表所做的變更。如需此行為的詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理]。
7. 在 `OnNavigatedTo` 事件處理常式中，取消註解對 `InitLocalStoreAsync` 的呼叫。如果您已完成[驗證教學課程](app-service-mobile-windows-store-dotnet-get-started-users.md)，則必須改為在 `AuthenticateAsync` 方法中執行此作業。
8. 在 `InsertTodoItem` 和 `UpdateCheckedTodoItem` 方法中取消註解 [PushAsync] 的呼叫，然後在 `ButtonRefresh_Click` 方法中取消註解 `SyncAsync` 的呼叫。
9. 在 `SyncAsync` 方法中，加入下列例外狀況處理常式︰
   
       private async Task SyncAsync()
       {
           String errorString = null;
   
           try
           {
               await App.MobileService.SyncContext.PushAsync();
               await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); 
           }
   
           catch (MobileServicePushFailedException ex)
           {
               errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                 ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
           }
           catch (Exception ex)
           {
               errorString = "Pull failed: " + ex.Message +
                 "\n\nIf you are still in an offline scenario, " +
                 "you can try your pull again when connected with your backend.";
           }
   
           if (errorString != null)
           {
               MessageDialog d = new MessageDialog(errorString);
               await d.ShowAsync();
           }
       }
   
   在離線的情況下，[PullAsync][PullAsync] 會導致 [MobileServicePushFailedException][MobileServicePushFailedException]，且 [PushResult.Status][Status] 為 [CancelledByNetworkError][CancelledByNetworkError]。當隱含推送嘗試先推送擱置中的更新再提取時，便會發生此例外狀況，然後失敗。如需詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理]。
10. 在 Visual Studio 中按 **F5** 鍵，以重新建置並執行用戶端應用程式。應用程式的運作方式和啟用離線同步處理之前的方式相同。不過，本機資料庫現在會填入可在離線案例下使用的資料。接下來，您將會建立離線案例，並使用本機儲存的資料來啟動應用程式。

## <a name="update-sync"></a>更新應用程式以使其與後端中斷連線
在本節中，您將中斷與行動應用程式後端的連線，以模擬離線狀態。當您新增資料項目時，您的例外狀況處理常式會指出應用程式處於離線模式。處於此狀態時，新項目會新增到本機存放區，並且會在推送接下來於連線狀態執行時，同步處理至行動應用程式後端。

1. 編輯共用專案中的 App.xaml.cs。註解化 **MobileServiceClient** 的初始化，並新增使用無效行動應用程式 URL 的以下幾行：
   
         public static MobileServiceClient MobileService =
                new MobileServiceClient("https://your-service.azurewebsites.fail");
   
    請注意，當應用程式也使用驗證時，這會導致登入失敗。您也可以透過停用裝置的 WiFi 和行動電話通訊網路，或使用飛航模式來示範離線行為。
2. 按 **F5** 以建置並執行應用程式。請注意，應用程式啟動後同步處理無法重新整理。
3. 輸入新項目，並注意每次您按一下 [儲存] 時，推送都會失敗並具有 [CancelledByNetworkError] 狀態。不過，新的 todo 項目在可推送至行動應用程式後端之前，都會存留在本機存放區中。
   
    在生產應用程式中，如果您隱藏這些例外狀況，用戶端應用程式的行為會如同它仍連線到行動應用程式後端。
4. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。
5. (選擇性) 在 Visual Studio 中，開啟 [伺服器總管]。瀏覽至 [Azure]-> [SQL Database] 中的資料庫。在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟]。現在您可以瀏覽至您的 SQL Database 資料表和其內容。確認後端資料庫中的資料沒有變更。
6. (選擇性) 使用 REST 工具 (例如 Fiddler 或 Postman) 來查詢您的行動後端 (使用表單 `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem` 中的 GET 查詢)。

## <a name="update-online-app"></a>更新應用程式以重新連接您的行動應用程式後端
在本節中，您會將應用程式重新連接至行動應用程式後端。您將藉此模擬應用程式在行動應用程式後端中，從離線狀態恢復為線上狀態的情境。當您第一次執行應用程式時，`OnNavigatedTo` 事件處理常式會呼叫 `InitLocalStoreAsync`。接著會呼叫 `SyncAsync` 來同步處理您的本機存放區與後端資料庫。因此，應用程式會嘗試於開機時同步處理。

1. 在共用專案中開啟 App.xaml.cs，取消註解先前的 `MobileServiceClient` 初始化，以使用正確的行動應用程式 URL。
2. 按 **F5** 鍵，以重新建置與執行應用程式。該應用程式會在 `OnNavigatedTo` 事件處理常式執行時使用推送與提取作業，同步處理您的本機變更與 Azure 行動應用程式後端。
3. (選擇性) 使用 SQL Server 物件總管或 REST 工具 (例如 Fiddler) 檢視已更新的資料。請注意，Azure 行動應用程式後端資料庫與本機存放區之間的資料已同步處理。
4. 在應用程式中，按一下幾個項目旁邊的核取方塊，以在本機存放區中完成它們。
   
   `UpdateCheckedTodoItem` 會呼叫 `SyncAsync`，以便與行動應用程式後端同步處理每個已完成的項目。`SyncAsync` 會呼叫推送與提取。不過，您應該注意**每當您針對用戶端進行變更的資料表執行提取時，一律會先自動執行用戶端同步處理內容推送**。這是為了確保本機存放區中的所有資料表和關聯性都保持一致。因此在此情況下，我們可以先移除對 `PushAsync` 的呼叫，因為執行提取時它會自動執行。如果您不注意，此行為會導致非預期的推送。如需此行為的詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理]。

## API 摘要
為了支援行動服務的離線功能，我們使用了 [IMobileServiceSyncTable] 介面，並初始化本機 SQLite 資料庫的 [MobileServiceClient.SyncContext][synccontext]。離線時，Mobile Apps 的一般 CRUD 作業運作方式，就如同應用程式仍處於連線狀態，而作業會對本機存放區執行。下列方法可用來同步處理本機存放區與伺服器︰

* **[PushAsync]**  
  因為這個方法是 [IMobileServicesSyncContext] 的成員，因此所有資料表的變更都會套用至後端。只有具有本機變更的記錄會傳送到伺服器。
* **[PullAsync]**   
  提取會從 [IMobileServiceSyncTable] 開始。當資料表中有追蹤的變更時，便會執行隱含推送，以確定本機存放區中的所有資料表和關聯性都維持一致。pushOtherTables 參數會控制是否在隱含推送中推送內容中的其他資料表。query 參數會採用 [IMobileServiceTableQuery&lt;U&gt;][IMobileServiceTableQuery] 或 OData 查詢字串來篩選傳回的資料。queryId 參數可用來定義增量同步處理。如需詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理](app-service-mobile-offline-data-sync.md#how-sync-works)。
* **[PurgeAsync]**  
  您的應用程式應定期呼叫這個方法，以清除本機存放區中的過時資料。當您需要清除任何尚未同步處理的變更時，請使用 force 參數。

如需這些概念的詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理](app-service-mobile-offline-data-sync.md#how-sync-works)。

## 其他資訊
下列主題提供其他關於 Mobile Apps 離線同步處理功能的背景資訊︰

* [Azure 行動應用程式中的離線資料同步處理]
* [雲端報導︰Azure 行動服務中的離線同步處理] \(注意︰此影片是針對行動服務，但離線同步處理的運作方式類似在 Azure Mobile Apps 中的方式)
* [Azure Friday：Azure 行動服務中離線啟用的應用程式]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Azure Mobile Apps 中的離線資料同步處理]: app-service-mobile-offline-data-sync.md
[Azure 行動應用程式中的離線資料同步處理]: app-service-mobile-offline-data-sync.md
[create a windows app]: app-service-mobile-windows-store-dotnet-get-started.md
[建立 Windows 應用程式]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[雲端報導︰Azure 行動服務中的離線同步處理]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday：Azure 行動服務中離線啟用的應用程式]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0824_2016-->