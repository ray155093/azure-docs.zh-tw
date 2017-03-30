---
title: "使用 Xamarin 和 Azure DocumentDB 建置行動應用程式 | Microsoft Docs"
description: "使用 Azure DocumentDB 建立 Xamarin iOS、Android 或 Forms 應用程式的教學課程。 DocumentDB 是速度驚人的全球級行動應用程式雲端資料庫。"
services: documentdb
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4a783397b038b7545bbbfc45813db3d9973c5e19
ms.lasthandoff: 03/22/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>使用 Xamarin 和 Azure DocumentDB 建置行動應用程式
大部分的行動應用程式都需要將資料儲存在雲端，而 Azure DocumentDB 正是適用於行動應用程式的雲端資料庫。 行動應用程式開發人員所需的一切盡在其中。 它是受到完整管理的 NoSQL 資料庫即服務，可依需求進行調整，而且不論使用者去到世界各地，它都能將資料默默地傳送到您的應用程式。 使用 [Azure DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md)，您可以讓 Xamarin 行動應用程式直接與 DocumentDB 互動，而不需要經過中間層。

在本文中，我們會提供教學課程來讓您了解如何使用 Xamarin 和 DocumentDB 建置行動應用程式。 您可以在 [GitHub 上的 Xamarin 和 DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) 中找到本教學課程的完整原始程式碼，包括如何管理使用者和權限。

## <a name="documentdb-capabilities-for-mobile-apps"></a>適用於行動應用程式的 DocumentDB 功能
DocumentDB 為行動應用程式開發人員提供了下列重要功能︰

![適用於行動應用程式的 DocumentDB 功能](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* 豐富的無結構描述資料查詢。 DocumentDB 會以無結構描述 JSON 文件的形式將資料儲存在異質集合中，並提供[豐富且快速的查詢](documentdb-sql-query.md)，讓您無須擔心結構描述或索引的問題。
* 快速。 有保證。 只需要幾毫秒就能使用 DocumentDB 讀取和寫入文件。 開發人員可以指定所需的輸送量，而且 DocumentDB 會以 99.99% 的 SLA 來接受它。
* 規模無限制。 您的 DocumentDB 集合可[隨著應用程式來成長](documentdb-partition-data.md)。 您可以從小型資料大小和每秒數百個要求來開始，然後成長到任意大小、每秒數千萬和數億個要求的輸送量，以及數 PB 的資料。
* 散布世界各地。 您的行動應用程式使用者總是忙個不停，經常在世界各地奔波。 DocumentDB 是[散布世界各地的資料庫](documentdb-distribute-data-globally.md)，只要在地圖上按一下，它就會將資料傳送給使用者，不論他身在何處。
* 內建豐富授權。 使用 DocumentDB 就能輕鬆實作熱門模式，例如[每位使用者的資料](https://aka.ms/documentdb-xamarin-todouser)或多位使用者共用的資料，而不需要複雜的自訂授權程式碼。
* 地理空間查詢。 許多行動應用程式目前都有提供地理情境體驗。 透過一流的[地理空間類型](documentdb-geospatial.md)支援，DocumentDB 可讓您輕鬆實現這些體驗。
* 二進位附件。 您的應用程式資料通常包含二進位 Blob。 原生的附件支援可讓您更輕鬆地使用 DocumentDB 來作為應用程式資料的集散中心。

## <a name="documentdb-and-xamarin-tutorial"></a>DocumentDB 和 Xamarin 教學課程
下列教學課程示範如何透過 5 個簡單的步驟，使用 Xamarin 和 DocumentDB 建置行動應用程式。 您可以在 [GitHub 上的 Xamarin 和 DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) 中找到本教學課程的完整原始程式碼。

### <a name="get-started"></a>開始使用
您可以輕鬆地開始使用 DocumentDB。 只要移至 Azure 入口網站，建立新的 DocumentDB 帳戶，然後移至 [快速入門] 索引標籤，並下載 Xamarin Forms「待辦事項」範例 (已連線到您的 DocumentDB 帳戶)。 

![適用於行動應用程式的 DocumentDB 快速入門](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

或者，如果您有現有的 Xamarin 應用程式，您可以直接新增此 [DocumentDB NuGet 套件](documentdb-sdk-dotnet-core.md)。 DocumentDB 支援 Xamarin.IOS、Xamarin.Android 和 Xamarin Forms 共用程式庫。

### <a name="work-with-data"></a>使用資料
您的資料記錄會以無結構描述 JSON 文件的形式儲存在 DocumentDB 的異質集合中。 您可以在相同的集合中使用不同結構儲存文件。

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

在您的 Xamarin 專案中，您可以使用語言整合式的無結構描述資料查詢︰

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>新增使用者
如同許多開始使用範例，您下載的 DocumentDB 範例會使用應用程式之程式碼中硬式編碼的主要金鑰來對服務驗證。 除了本機模擬器外，最好不要在您想要執行的應用程式上這麼做。 如果攻擊者得到主要金鑰，整個 DocumentDB 帳戶的所有資料都會外洩。 相反地，我們想讓應用程式只能存取已登入之使用者的記錄。 DocumentDB 可讓開發人員對應用程式授與集合、依資料分割索引鍵群組的一組文件或特定文件的讀取或讀取/寫入權限。 

例如，以下是如何將我們的待辦事項清單應用程式修改為多位使用者的「待辦事項清單」應用程式的步驟︰ 

* 使用 Facebook、Active Directory 或其他任何提供者對應用程式新增登入。
* 使用 /userId 作為資料分割索引鍵來建立 DocumentDB UserItems 集合。 為集合指定資料分割索引鍵，就能讓 DocumentDB 隨著應用程式使用者人數的成長來無限制地擴充，同時提供快速的查詢。
* 新增 DocumentDB 資源權杖訊息代理程式，這個簡單的 Web API 可驗證使用者，並對已登入的使用者發出只能存取使用者資料分割內文件的短期權杖。 在此範例中，我們將資源權杖訊息代理程式裝載在 App Service 中。
* 修改應用程式以使用 Facebook 向資源權杖訊息代理程式驗證，並為已登入的 Facebook 使用者要求資源權杖，然後存取 UserItems 集合中的使用者資料。  

您可以在 [GitHub 上的資源權杖訊息代理程式](http://aka.ms/documentdb-xamarin-todouser)中找到此模式的完整程式碼範例。 下圖可說明解決方案︰

![DocumentDB 使用者和權限訊息代理程式](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

現在，如果我們想讓兩位使用者存取同一個「待辦事項清單」，我們可以在資源權杖訊息代理程式的存取權杖中新增額外的權限。

### <a name="scale-on-demand"></a>依需求進行調整
DocumentDB 是受管理的資料庫即服務。 隨著使用者人數成長，您不必為佈建 VM 或增加核心而煩惱。 您只需要告訴 DocumentDB 您的應用程式每秒需要多少作業數 (輸送量)。 您可以透過入口網站的 [調整] 索引標籤，使用稱為每秒要求單位數 (RU) 的量值來指定輸送量。 例如，對 1 KB 文件進行的讀取作業需要 1 RU。 您也可以為「輸送量」計量新增警示來監視流量成長，並設計程式以在引發警示時變更輸送量。

![DocumentDB 依需求調整輸送量](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>進入全球級別
隨著您的應用程式日益受到歡迎，您可能會吸引到全球各地的使用者。 或者，您可能只是不想在隕石撞擊 DocumentDB 集合建立所在之 Azure 資料中心的情況下猝不及防。 使用您的 DocumentDB 帳戶進入 Azure 入口網站，並在地圖上按一下滑鼠，讓您的資料持續複寫到世界各地任意數目的區域。 這可確保不論使用者身在何處都能使用您的資料，而且您可以新增容錯移轉原則以備不時之需。

![跨地理區域的 DocumentDB 調整](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

恭喜！ 您已完成解決方案，並使用 Xamarin 和 DocumentDB 建置了行動應用程式。 類似模式可用於使用 DocumentDB JavaScript SDK 的 Cordova 應用程式，以及使用 DocumentDB REST API 的原生 iOS/Android 應用程式。

## <a name="next-steps"></a>後續步驟
* 檢視 [GitHub 上的 Xamarin 和 DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) 原始程式碼。
* 下載 [DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md)。
* 尋找更多 [.NET 應用程式](documentdb-dotnet-samples.md)的程式碼範例。
* 了解 [DocumentDB 的豐富查詢功能](documentdb-sql-query.md)。
* 了解 [DocumentDB 中的地理空間支援](documentdb-geospatial.md)。




