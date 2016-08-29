<properties 
	pageTitle="DocumentDB .NET API 和 SDK | Microsoft Azure" 
	description="全面了解 .NET API 和 SDK，包括發行日期、停用日期及 DocumentDB .NET SDK 每個版本之間的變更。" 
	services="documentdb" 
	documentationCenter=".net" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="rnagpal"/>

# DocumentDB API 和 SDK 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## DocumentDB .NET API 和 SDK

<table>
<tr><td>**SDK 下載**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**API 文件**</td><td>[.NET API 參考文件](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**範例**</td><td>[.NET 程式碼範例](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**開始使用**</td><td>[開始使用 DocumentDB .NET SDK](documentdb-get-started.md)</td></tr>
<tr><td>**Web 應用程式教學課程**</td><td>[使用 DocumentDB 開發 Web 應用程式](documentdb-dotnet-application.md)</td></tr>
<tr><td>**目前支援的架構**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## 版本資訊

### <a name="1.9.3"/>[1\.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)
> [AZURE.IMPORTANT] 查詢分割集合時，您可能會收到 System.NotSupportedException。若要避免這個錯誤，請在 [建置] 索引標籤上取消選取專案屬性視窗中的 [建議使用 32 位元] 選項。

  - 已修正造成長時間執行的查詢失敗，並伴隨「授權權杖目前無效」錯誤的問題。
  - 已修正會從跨資料分割的排名/排序依據查詢中移除原始 SqlParameterCollection 的問題。

### <a name="1.9.2"/>[1\.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - 已新增分割集合的平行查詢支援。
  - 已新增分割集合的跨資料分割 ORDER BY 和 TOP 查詢支援。
  - 已修正使用 DocumentDB Nuget 封裝參照來參考 DocumentDB 專案時，遺失所需的 DocumentDB.Spatial.Sql.dll 與 Microsoft.Azure.Documents.ServiceInterop.dll 參照。
  - 已修正在 LINQ 中使用使用者定義的函式時，使用不同類型參數的能力。
  - 已修正廣域複寫帳戶中的的錯誤，此錯誤會使得 Upsert 呼叫導向讀取位置而非寫入位置。
  - 已在遺失的 IDocumentClient 介面加入方法：
      - UpsertAttachmentAsync 方法，會接受 mediaStream 及選項做為參數
      - CreateAttachmentAsync 方法，會接受選項做為參數
      - CreateOfferQuery 方法，會接受 querySpec 做為參數
  - 已解除密封 IDocumentClient 介面中公開的公用類別。

### <a name="1.8.0"/>[1\.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - 新增對多重區域資料庫帳戶的支援。
  - 新增在已節流處理的要求上進行重試的支援。使用者可以藉由設定 ConnectionPolicy.RetryOptions 屬性，來自訂重試次數和等待時間上限。
  - 新增新的 IDocumentClient 介面，其中會定義所有 DocumenClient 屬性和方法的簽章。做為此變更的一部分，也將建立 IQueryable 和 IOrderedQueryable 的擴充方法變更為 DocumentClient 類別本身上的方法。
  - 新增組態選項，以針對指定的 DocumentDB 端點 URI 設定 ServicePoint.ConnectionLimit。使用 ConnectionPolicy.MaxConnectionLimit 來變更預設值 (已設為 50)。
  - 已淘汰 IPartitionResolver 及其實作。現在不支援 IPartitionResolver。建議您針對更高的儲存體和輸送量使用分割集合。


### <a name="1.7.1"/>[1\.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - 根據會接受 RequestOptions 做為參數的 ExecuteStoredProcedureAsync 方法，新加入 URI 多載。
  
### <a name="1.7.0"/>[1\.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - 新加入文件的存留時間 (TTL) 支援。

### <a name="1.6.3"/>[1\.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - 修正 .NET SDK 的 Nuget 封裝錯誤，可供封裝為 Azure 雲端服務解決方案的一部分。
  
### <a name="1.6.2"/>[1\.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - 實作[分割集合](documentdb-partition-data.md)和[使用者定義的效能等級](documentdb-performance-levels.md)。

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[已修正]** 查詢 DocumentDB 端點時擲回：「System.Net.Http.HttpRequestException：將內容複製到資料流時發生錯誤」。

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - 擴充的 LINQ 支援包括新的分頁、條件式運算式以及範圍比較的運算子。
    - Take 運算子：可啟用 LINQ 中的 SELECT TOP 行為
    - CompareTo 運算子：可啟用字串範圍比較
    - 條件式 (?) 與聯合運算子 (??)
  - **[已修正]** 將模型投射與 LINQ 查詢中的 Where-In 結合使用時發生 ArgumentOutOfRangeException。[#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[已修正]** 如果 Select 不是最後一個運算式，LINQ 提供者會假設沒有任何預測，並會產生不正確的 SELECT *。[#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - 實作 Upsert、新增 UpsertXXXAsync 方法
 - 所有要求的效能改進
 - LINQ 提供者支援字串的條件式、聯合和 CompareTo 方法
 - **[已修正]** LINQ 提供者 --> 在 List 上實作 Contains 方法，以產生與 IEnumerable 和 Array 上相同的 SQL
 - **[已修正]** 重試時，BackoffRetryUtility 會再次使用相同的 HttpRequestMessage，而非建立新的
 - **[已過時]** UriFactory.CreateCollection --> 現應使用 UriFactory.CreateDocumentCollection
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[已修正]** 使用非 en 文化特性資訊時 (例如 nl-NL 等) 的當地語系化問題。
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - 以識別碼為基礎的路由
    - 新的 UriFactory 協助程式，協助建構以識別碼為基礎的資源連結
    - DocumentClient 上新的多載可接受 URI
  - LINQ 中新增用於地理空間的 IsValid() 和 isvaliddetailed ()
  - 增強的 LINQ 提供者支援
    - **算術** - Abs、Acos、Asin、Atan、Ceiling、Cos、Exp、Floor、Log、Log10、Pow、Round、Sign、Sin、Sqrt、Tan、Truncate
    - **字串** - Concat、Contains、EndsWith、IndexOf、Count、ToLower、TrimStart、Replace、Reverse、TrimEnd、StartsWith、SubString、ToUpper
    - **陣列** - Concat、Contains、Count
    - **IN** 運算子

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - 新增支援來修改索引編製原則
    - DocumentClient 中新的 ReplaceDocumentCollectionAsync 方法
    - ResourceResponse<T> 中新的 IndexTransformationProgress 屬性可追蹤索引原則變更的百分比進度
    - DocumentCollection.IndexingPolicy 現在可變動
  - 新增空間索引編製和查詢的支援
    - 新的 Microsoft.Azure.Documents.Spatial 命名空間，可序列化/還原序列化空間類型，例如 Point 和 Polygon
    - 新的 SpatialIndex 類別，可對儲存在 DocumentDB 中的 GeoJSON 資料編製索引
  - **[已修正]**：從 LINQ 運算式產生的 SQL 查詢不正確 [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- 對 Newtonsoft.Json v5.0.7 的相依性
- 變更為支援 Order By
  - LINQ 提供者支援 OrderBy() 或 OrderByDescending()
  - IndexingPolicy 支援 Order By
  
		**NB: Possible breaking change** 
  
    	If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- 支援使用新的 HashPartitionResolver 和 RangePartitionResolver 類別及 IPartitionResolver 來分割資料
- DataContract 序列化
- LINQ 提供者中的 Guid 支援
- LINQ 中的 UDF 支援

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- GA SDK

> [AZURE.NOTE]
NuGet 封裝名稱在預覽和 GA 之間已變更。我們從 **Microsoft.Azure.Documents.Client** 移至 **Microsoft.Azure.DocumentDB** <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- 預覽 SDK [已過時]

## 發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月**之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [AZURE.WARNING]
所有 **1.0.0** 之前的 Azure DocumentDB SDK for .NET 版本都將於 **2016 年 2 月29 日**時淘汰。
 
<br/>
 
| 版本 | 發行日期 | 停用日期 
| ---	  | ---	         | ---
| [1\.9.3](#1.9.3) | 2016 年 8 月 15 日 |--- 
| [1\.9.2](#1.9.2) | 2016 年 7 月 23 日 |--- 
| 1.9.1 | 已過時 |--- | 1.9.0 | 已過時 |--- 
| [1\.8.0](#1.8.0) | 2016 年 6 月 14 日 |--- 
| [1\.7.1](#1.7.1) | 2016 年 5 月 6 日 |--- 
| [1\.7.0](#1.7.0) | 2016 年 4 月 26 日 |--- 
| [1\.6.3](#1.6.3) | 2016 年 4 月 28 日 |--- 
| [1\.6.2](#1.6.2) | 2016 年 3 月 29 日 |--- 
| [1\.5.3](#1.5.3) | 2016 年 2 月 19 日 |--- 
| [1\.5.2](#1.5.2) | 2015 年 12 月 14 日 |--- 
| [1\.5.1](#1.5.1) | 2015 年 11 月 23 日 |--- 
| [1\.5.0](#1.5.0) | 2015 年 10 月 5 日 |--- 
| [1\.4.1](#1.4.1) | 2015 年 8 月 25 日 |--- 
| [1\.4.0](#1.4.0) | 2015 年 8 月 13 日 |--- 
| [1\.3.0](#1.3.0) | 2015 年 8 月 5 日 |--- 
| [1\.2.0](#1.2.0) | 2015 年 7 月 6 日 |--- 
| [1\.1.0](#1.1.0) | 2015 年 4 月 30 日 |--- 
| [1\.0.0](#1.0.0) | 2015 年 4 月 8 日 |--- 
| [0\.9.3 發行前版本](#0.9.x-preview) | 2015 年 3 月 12 日 | 2016 年 2 月 29 日 
| [0\.9.2 發行前版本](#0.9.x-preview) | 2015 年 1 月 | 2016 年 2 月 29 日 
| [.9.1 發行前版本](#0.9.x-preview) | 2014 年 10 月 13 日 | 2016 年 2 月 29 日 
| [0\.9.0 發行前版本](#0.9.x-preview) | 2014 年 8 月 21 日 | 2016 年 2 月 29 日

## 常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱

若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。

<!---HONumber=AcomDC_0817_2016-->