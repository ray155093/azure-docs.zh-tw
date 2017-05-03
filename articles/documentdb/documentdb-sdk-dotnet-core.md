---
title: "Azure DocumentDB .NET Core API、SDK 和資源 | Microsoft Docs"
description: "全面了解 .NET Core API 和 SDK，包括發行日期、停用日期及 DocumentDB .NET Core SDK 每個版本之間的變更。"
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/19/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: d5552615bfa663f4604e29839ecf8f5fc25c328f
ms.lasthandoff: 04/21/2017


---
# <a name="documentdb-net-core-sdk-release-notes-and-resources"></a>DocumentDB .NET Core SDK︰版本資訊與資源
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK 下載**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API 文件**</td><td>[.NET API 參考文件](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**範例**</td><td>[.NET 程式碼範例](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**開始使用**</td><td>[開始使用 DocumentDB .NET Core SDK](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Web 應用程式教學課程**</td><td>[使用 DocumentDB 開發 Web 應用程式](documentdb-dotnet-application.md)</td></tr>

<tr><td>**目前支援的架構**</td><td>[.NET Standard 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>版本資訊

DocumentDB .NET Core SDK 有與最新版 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 類似的功能。

> [!NOTE] 
> DocumentDB .NET Core SDK 與「通用 Windows 平台」(UWP) 應用程式尚未相容。 如果您有興趣了解可支援 UWP 應用程式的 .NET Core SDK，請傳送電子郵件至 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com)。

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* 修正將在彙總查詢之 FeedOptions 中提供的 PartitionKey 值忽略的問題。
* 修正在執行移動途中跨資料分割 Order By 查詢期間，進行資料分割管理的透明處理時發生的問題。

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* 已修正在 ASP.NET 內容內部使用時會在某些非同步 API 中造成死結的問題。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 修正來讓 SDK 在某些情況下能夠更有彈性地進行自動容錯移轉。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* 修正偶爾會造成「WebException: 無法解析遠端名稱」的錯誤。
* 透過針對 ReadDocumentAsync API 新增多載，以新增直接讀取具類型文件的支援。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 新增彙總查詢的 LINQ 支援 (COUNT、MIN、MAX、SUM 和 AVG)。
* 針對使用事件處理常式所造成的 ConnectionPolicy 物件，修正記憶體流失問題。
* 修正使用 ETag 時 UpsertAttachmentAsync 無法運作的問題。
* 修正根據字串欄位排序時交叉資料分割排序依據查詢接續無法運作的問題。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 新增彙總查詢的支援 (COUNT、MIN、MAX、SUM 和 AVG)。 請參閱[彙總支援](documentdb-sql-query.md#Aggregates)。
* 已將分割區集合的最小輸送量從 10,100 RU/s 降低為 2500 RU/s。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

DocumentDB .NET Core SDK 可讓您建置快速、跨平台的 [ASP.NET Core](https://www.asp.net/core) 和 [.NET Core](https://www.microsoft.com/net/core#windows) 應用程式，以在 Windows、Mac 和 Linux 上執行。 最新版 DocumentDB .NET Core SDK 與 [Xamarin](https://www.xamarin.com) 完全相容，可用來建置以 iOS、Android 及 Mono (Linux) 為目標的應用程式。  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

DocumentDB .NET Core Preview SDK 可讓您建置快速、跨平台的 [ASP.NET Core](https://www.asp.net/core) 和 [.NET Core](https://www.microsoft.com/net/core#windows) 應用程式，在 Windows、Mac 和 Linux 上執行。

DocumentDB .NET Core Preview SDK 有與最新版本 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 的功能類似的功能，並且支援下列項目︰
* 所有[連接模式](documentdb-performance-tips.md#networking)︰閘道器模式、直接 TCP 和 Direct HTTPs。 
* 所有[一致性層級](documentdb-consistency-levels.md)︰強式、工作階段、限定過期和最終。
* [資料分割的集合](documentdb-partition-data.md)。 
* [多重區域資料庫帳戶和異地複寫](documentdb-distribute-data-globally.md)。

如果您有與此 SDK 相關的問題，請張貼至 [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)，或是在 [Github 儲存機制](https://github.com/Azure/azure-documentdb-dotnet/issues)中提出問題。 

## <a name="release--retirement-dates"></a>發行和停用日期

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [1.2.2](#1.2.2) |2017 年 4 月 19 日 |--- |
| [1.2.1](#1.2.1) |2017 年 3 月 29 日 |--- |
| [1.2.0](#1.2.0) |2017 年 3 月 25 日 |--- |
| [1.1.2](#1.1.2) |2017 年 3 月 20 日 |--- |
| [1.1.1](#1.1.1) |2017 年 3 月 14 日 |--- |
| [1.1.0](#1.1.0) |2017 年 2 月 16 日 |--- |
| [1.0.0](#1.0.0) |2016 年 12 月 21 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2016 年 11 月 15 日 |2016 年 12 月 31 日 |

## <a name="see-also"></a>另請參閱
若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。 


