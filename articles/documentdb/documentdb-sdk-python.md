<properties 
	pageTitle="DocumentDB Python API 和 SDK | Microsoft Azure" 
	description="了解所有 Python API 和 SDK 相關資訊，包括 發行日期、停用日期及 DocumentDB Python SDK 每個版本之間的變更。" 
	services="documentdb" 
	documentationCenter="python" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>

# DocumentDB API 和 SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## DocumentDB Python API 和 SDK

<table>
<tr><td>**下載 SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**API 文件**</td><td>[Python API 參考文件](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**SDK 安裝指示**</td><td>[Python SDK 安裝指示](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**提供給 SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**開始使用**</td><td>[開始使用 Python SDK](documentdb-python-application.md)</td></tr>
<tr><td>**目前支援的平台**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## 版本資訊

### <a name="1.9.0"/>[1\.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- 新加入已節流處理要求的重試原則支援。(已節流處理的要求會收到要求率太大的例外狀況，即錯誤碼 429。) 根據預設，發生錯誤碼 429 時，DocumentDB 會遵守回應標頭中的 retryAfter 時間，並針對每個要求重試九次。如果您想要忽略伺服器在多次重試之間傳回的 retryAfter 時間，現在可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中設定固定的重試間隔時間。DocumentDB 現在會針對每個節流處理中的要求等候最多 30 秒 (不論重試計數為何)，並傳回包含錯誤碼 429 的回應。您也可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中覆寫該時間。

- DocumentDB 現在會傳回 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 做為每個要求的回應標頭，其代表節流重試計數和要求歷經多次重試的累積時間。

- 移除 RetryPolicy 類別和 document\_client 類別上公開的對應屬性 (retry\_policy)，改為引進公開 ConnectionPolicy 類別上的 RetryOptions 屬性，它可以用來覆寫某些預設的重試選項。

### <a name="1.8.0"/>[1\.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - 新增對多重區域資料庫帳戶的支援。

### <a name="1.7.0"/>[1\.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- 新加入文件的存留時間 (TTL) 功能支援。

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- 伺服器端資料分割相關錯誤修正，允許在 partitionkey 路徑中使用特殊字元。

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- 實作[分割集合](documentdb-partition-data.md)和[使用者定義的效能等級](documentdb-performance-levels.md)。

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- 新增「雜湊和範圍」分割區解析程式來協助將應用程式跨多個分割區分區。

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- 實作 Upsert。已新增新的 UpsertXXX 方法以支援 Upsert 功能。
- 實作以識別碼為基礎的路由。不需變更公用 API，所有變更皆為內部變更。

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- 支援地理空間索引。
- 驗證所有資源的識別碼屬性。資源的識別碼不能包含 ?、/、#、\\ 等字元，或在結尾處使用空格。
- 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- 實作 V2 索引原則。

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- 支援 Proxy 連線。

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK。

## 發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [AZURE.WARNING]
適用於 Python 之所有 **1.0.0** 之前的 Azure DocumentDB SDK 版本都將於 **2016 2 月 29 日停用**。

<br/>

| 版本 | 發行日期 | 停用日期 
| ---	  | ---	         | ---
| [1\.9.0](#1.9.0) | 2016 年 7 月 7 日 |--- 
| [1\.8.0](#1.8.0) | 2016 年 6 月 14 日 |--- 
| [1\.7.0](#1.7.0) | 2016 年 4 月 26 日 |--- 
| [1\.6.1](#1.6.1) | 2016 年 4 月 8 日 |--- 
| [1\.6.0](#1.6.0) | 2016 年 3 月 29 日 |--- 
| [1\.5.0](#1.5.0) | 2016 年 1 月 3 日 |--- 
| [1\.4.2](#1.4.2) | 2015 年 10 月 6 日 |--- 
| [1\.4.1](#1.4.1) | 2015 年 10 月 6 日 |--- 
| [1\.2.0](#1.2.0) | 2015 年 8 月 6 日 |--- 
| [1\.1.0](#1.1.0) | 2015 年 7 月 9 日 |--- 
| [1\.0.1](#1.0.1) | 2015 年 5 月 25 日 |--- 
| [1\.0.0](#1.0.0) | 2015 年 4 月 7 日 |--- 
| 0.9.4-發行前版本 | 2015 年 1 月 14 日 | 2016 年 2 月 29 日 
| 0.9.3-發行前版本 | 2014 年 12 月 9 日 | 2016 年 2 月 29 日 
| 0.9.2-發行前版本 | 2014 年 11 月 25 日 | 2016 年 2 月 29 日 
| 0.9.1-發行前版本 | 2014 年 9 月 23 日 | 2016 年 2 月 29 日 
| 0.9.0-發行前版本 | 2014 年 8 月 21 日 | 2016 年 2 月 29 日

## 常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱

若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。

<!---HONumber=AcomDC_0810_2016---->