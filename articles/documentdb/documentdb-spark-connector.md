---
title: "將 Apache Spark 連線至 Azure DocumentDB | Microsoft Docs"
description: "使用這個教學課程來了解 Azure DocumentDB Spark 連接器；這個連接器可讓您將 Apache Spark 連線至 Azure DocumentDB，以對 Microsoft 針對雲端所設計的多租用戶全域分散式資料庫系統執行分散式彙總和資料科學。"
keywords: Apache Spark
services: documentdb
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: denlee
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 3d534ed667a8d8d012fa35152ae1bdb873e9672d
ms.lasthandoff: 04/06/2017


---

# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-documentdb-connector"></a>使用 Spark to DocumentDB 連接器加速即時巨量資料分析

Spark to DocumentDB 連接器可讓 Azure DocumentDB 作為 Apache Spark 作業的輸入來源或輸出來源。 將 [Spark](http://spark.apache.org/) 連接至 [DocumentDB](https://azure.microsoft.com/services/documentdb/) 可加速您解決快速移動資料科學的問題，而且使用 DocumentDB 可以快速地保存和查詢資料。 Spark to DocumentDB 連接器會有效率地利用原生 DocumentDB 受管理索引，並在執行分析時啟用可更新資料行、針對快速變更全域分散式資料的向下推展述詞篩選，範圍包括 IoT、資料科學和分析案例。 

## <a name="download"></a>下載

請從 GitHub 的 [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark/) 存放庫下載 Spark to DocumentDB 連接器 (預覽) 開始。

## <a name="connector-components"></a>連接器元件

連接器使用下列元件：

* [DocumentDB](http://documentdb.com) 是 Microsoft 的多租用戶[全域散發資料庫系統](documentdb-distribute-data-globally.md)，專為雲端設計。 不管有多少地理區域，DocumentDB 都能讓客戶佈建及彈性調整輸送量與儲存空間。 此服務可確保向開發人員提供第 99 個百分位數的保證低延遲、保證 99.99% 的可用性，以及[多個定義完善的一致性模型](documentdb-consistency-levels.md)。

* [Apache Spark](http://spark.apache.org/) 是功能強大的開放原始碼處理引擎，專為速度、易用性和精密分析所打造。 

* [Azure HDInsight 上的 Apache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)。 您可以使用 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) 在雲端部署適用於任務關鍵性部署的 Apache Spark。

正式支援的版本：

| 元件 | 版本 |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| Azure DocumentDB Java SDK | 1.9.6 |
 
本文將協助您執行 Python (透過 pyDocumentDB) 和 scala 介面的一些簡單範例。

有兩種方法可以連線 Apache Spark 與 Azure DocumentDB：
- 透過 [Azure DocumentDB Python SDK](https://github.com/Azure/azure-documentdb-python) 使用 pyDocumentDB。
- 使用 [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java)，建立以 Java 為基礎的 Spark to DocumentDB 連接器。

## <a name="pydocumentdb-implementation"></a>pyDocumentDB 實作 
目前 [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python) 可讓我們將 Spark 連線到 DocumentDB，如下圖所示：

![透過 pyDocumentDB 的 Spark to DocumentDB 資料流程](./media/documentdb-spark-connector/azure-documentdb-spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>pyDocumentDB 實作的資料流程

資料流程如下︰

1. 透過 pyDocumentDB，從 Spark 主要節點連線到 DocumentDB 閘道節點。  請注意，使用者只需要指定 Spark 和 DocumentDB 連線，就會連線到個別主要和閘道節點。
2. 會針對 DocumentDB (透過閘道節點) 執行查詢，而查詢接著會對資料節點中集合的分割區執行查詢。 這些查詢的回應會傳回給閘道節點，並將該結果集傳回給 Spark 主要節點。
3. 任何後續查詢 (例如，針對 Spark DataFrame) 都會傳送到 Spark 背景工作角色節點，以進行處理。

重點在於，只有 Spark 主要節點和 DocumentDB 閘道節點才能進行 Spark 與 DocumentDB 之間的通訊。  查詢的執行速度很快，因為傳輸層介於這兩個節點之間。

### <a name="installing-pydocumentdb"></a>安裝 pyDocumentDB
您可以使用 **pip**，在驅動程式節點上安裝 pyDocumentDB，例如：

```
pip install pyDocumentDB
```


### <a name="connecting-spark-to-documentdb-via-pydocumentdb"></a>透過 pyDocumentDB 將 Spark 連線到 DocumentDB 
接著，基於通訊傳輸的簡單性，使用 pyDocumentDB 從 Spark to DocumentDB 執行查詢相當簡單。

下列程式碼片段示範如何在 Spark 內容內使用 pyDocumentDB。

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery 
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to DocumentDB 
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==' 
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

如程式碼片段所述：

* DocumentDB Python SDK 包含內含慣用位置的所有必要連線參數 (也就是，選擇依何種優先順序的何種讀取複本)。
*  匯入必要程式庫，並設定 **masterKey** 和 **host** 以建立 DocumentDB *client* (**pydocumentdb.document_client**)。


### <a name="executing-spark-queries-via-pydocumentdb"></a>透過 pyDocumentDB 執行 Spark 查詢
下列範例會使用上一個程式碼片段中，利用指定的唯讀金鑰建立的 DocumentDB 執行個體。  下列程式碼片段會連線至執行查詢的 **airports.codes** 集合 (在先前指定的 DoctorWho 帳戶中)，以擷取華盛頓州的機場城市。 

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the DocumentDB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

透過 **query** 執行查詢之後，結果是轉換成 Python 清單的 **query_iterable.QueryIterable**。 使用下列程式碼，可以輕鬆地將 Python 清單轉換成 Spark DataFrame︰

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-documentdb"></a>為什麼要使用 pyDocumentDB 將 Spark 連線到 DocumentDB？
使用 pyDocumentDB 將 Spark 連線到 DocumentDB 一般適用於下列情況：

* 您想要使用 python。
* 您要將相當小的結果集從 DocumentDB 傳回至 Spark。  請注意，在 DocumentDB 內的基礎資料集可能相當大。 甚至，您要將篩選條件套用至 DocumentDB 來源，即執行述詞篩選條件。  

## <a name="spark-to-documentdb-connector"></a>Spark to DocumentDB 連接器

Spark to DocumentDB 連接器會利用 [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java)，並在 Spark 背景工作角色節點與 DocumentDB 之間移動資料，如下圖所示：

![Spark to DocumentDB 連接器中的資料流程](./media/documentdb-spark-connector/azure-documentdb-spark-connector.png)

資料流程如下︰

1. 從 Spark 主要節點連線到 DocumentDB 閘道節點，可取得分割區對應。  請注意，使用者只需要指定 Spark 和 DocumentDB 連線，就會連線至個別主要和閘道節點。
2. 此資訊會提供回 Spark 主要節點。  此時，您應該可以剖析查詢，來判斷您需要存取 DocumentDB 內的哪些分割區 (和其位置)。
3. 此資訊會傳輸到 Spark 背景工作角色節點 ...
4. 因此，允許 Spark 背景工作角色節點直接連線到 DocumentDB 分割區，來擷取所需的資料，並將資料帶回 Spark 背景工作角色節點內的 Spark 分割區。

重點在於 Spark 與 DocumentDB 之間的通訊相當快，因為資料移動是在 Spark 背景工作角色節點與 DocumentDB 資料節點 (分割區) 之間進行。

### <a name="building-the-spark-to-documentdb-connector"></a>建置 Spark to DocumentDB 連接器
目前，連接器專案會使用 maven。 若要建置沒有相依性的連接器，您可以執行︰
```
mvn clean package
```
您也可以下載 *releases* 資料夾中的最新版 jar。

### <a name="including-the-azure-documentdb-spark-jar"></a>包括 Azure DocumentDB Spark JAR
執行任何程式碼之前，您需要先納入 Azure DocumentDB Spark JAR。  如果您是使用 **spark-shell**，則可以使用 **--jars** 選項來納入 JAR。  

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1-jar-with-dependencies.jar
```

或者，如果您想要執行不含相依性的 jar：

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1.jar,/$location/azure-documentdb-1.9.6.jar
```

如果您是使用 Notebook 服務 (例如 Azure HDInsight Jupyter Notebook 服務)，則可以使用 **spark magic** 命令：

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.9.6.jar","wasb:///example/jars/azure-documentdb-spark-0.0.1.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

**jars** 命令可讓您納入 **azure-documentdb-spark** 所需的兩個 jar (本身和 Azure DocumentDB Java SDK)，並排除 **scala-reflect**，讓它不要干擾進行的 Livy 呼叫 (Jupyter Notebook > Livy > Spark)。

### <a name="connecting-spark-to-documentdb-using-the-connector"></a>使用連接器將 Spark 連線到 DocumentDB
雖然通訊傳輸有些複雜，但是使用連接器執行從 Spark 到 DocumentDB 的查詢相當快。

下列程式碼片段示範如何在 Spark 內容內使用連接器。

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))
 
// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

如程式碼片段所述：

- **azure-documentdb-spark** 包含內含慣用位置的所有必要連線參數 (例如，選擇依何種優先順序的何種讀取複本)。
- 只需要匯入必要程式庫，並設定 masterKey 和 host 以建立 DocumentDB client。

### <a name="executing-spark-queries-via-the-connector"></a>透過連接器執行 Spark 查詢

下列範例會使用上一個程式碼片段中，利用指定的唯讀金鑰而建立的 DocumentDB 執行個體。 下列程式碼片段會連線至執行查詢的 DepartureDelays.flights_pcoll 集合 (在先前指定的 DoctorWho 帳戶中)，以擷取從西雅圖出發之航班的航班延遲資訊。

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-documentdb-connector-implementation"></a>為何要使用 Spark to DocumentDB 連接器實作？

使用連接器將 Spark 連線到 DocumentDB 一般適用於下列情況：

* 您想要使用 Scala (並將它更新成包括 Python 包裝函式，如[問題 3︰新增 Python 包裝函式和範例](https://github.com/Azure/azure-documentdb-spark/issues/3) (英文) 中所述)。
* 您有要在 Apache Spark 與 DocumentDB 之間傳送的大量資料。

為了讓您了解查詢效能差異，請參閱[查詢測試回合 Wiki](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs) (英文)。

## <a name="distributed-aggregation-example"></a>分散式彙總範例
本節提供一些範例，示範如何搭配使用 Apache Spark 與 Azure DocumentDB 來執行分散式彙總和分析。  請注意，Azure DocumentDB 已支援彙總 (如[使用 Azure DocumentDB 的 Planet Scale 彙總部落格](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) (英文) 中所述)，因此，這裡說明如何使用 Apache Spark 進入下個層級。

請注意，這些彙總都會參考 [Spark to DocumentDB 連接器 Notebook](https://github.com/Azure/azure-documentdb-spark/blob/master/samples/notebooks/Spark-to-DocumentDB_Connector.ipynb) (英文)。

### <a name="connecting-to-flights-sample-data"></a>連線至航班範例資料
在這些彙總範例中，我們將存取儲存在 **DoctorWho** DocumentDB 資料庫中的一些航班效能資料。  若要與它連線，您需要使用下列程式碼片段︰

```
// Import Spark to DocumentDB connector
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Connect to DocumentDB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))

// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

我們也會使用此程式碼片段執行基底查詢，以將想要的一組篩選過的資料從 DocumentDB 傳送到 Spark (後者可以執行分散式彙總)。  在此情況下，我們要求從西雅圖 (SEA) 出發的航班資料。

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

已從 Jupyter Notebook 服務執行查詢時，會產生下列結果。  請注意，所有程式碼片段都是泛型的，不是任何服務特有的。

### <a name="running-limit-and-count-queries"></a>執行 LIMIT 和 COUNT 查詢
就像在 SQL/Spark SQL 中使用一樣，讓我們開始使用 **LIMIT** 查詢︰

![Spark LIMIT 查詢](./media/documentdb-spark-connector/azure-documentdb-spark-sql-query.png)

下一個查詢是簡單且快速的 **COUNT** 查詢︰

![Spark COUNT 查詢](./media/documentdb-spark-connector/azure-documentdb-spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY 查詢
在這個下一組中，我們現在可以對 DocumentDB 資料庫輕鬆地執行 **GROUP BY** 查詢：

```
select destination, sum(delay) as TotalDelays 
from originSEA 
group by destination 
order by sum(delay) desc limit 10
```

![Spark GROUP BY 查詢圖形](./media/documentdb-spark-connector/azure-documentdb-group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>DISTINCT, ORDER BY 查詢
以下是 **DISTINCT, ORDER BY** 查詢：

![Spark GROUP BY 查詢圖形](./media/documentdb-spark-connector/azure-documentdb-order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>繼續分析航班資料
您可以使用下列範例查詢來繼續分析航班資料︰

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>從西雅圖出發的前 5 個延遲目的地 (城市)
```
select destination, sum(delay) 
from originSEA
where delay < 0 
group by destination 
order by sum(delay) limit 5
```
![Spark 前幾個延遲圖形](./media/documentdb-spark-connector/azure-documentdb-top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>計算從西雅圖出發的目的地城市的中間延遲
```
select destination, percentile_approx(delay, 0.5) as median_delay 
from originSEA
where delay < 0 
group by destination 
order by percentile_approx(delay, 0.5)
```

![Spark 中間幾個延遲圖形](./media/documentdb-spark-connector/azure-documentdb-median-delays-graph.png)

## <a name="next-steps"></a>後續步驟

如果您還沒有，則請從 [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark) GitHub 存放庫下載 Spark to DocumentDB 連接器，並探索存放庫中的其他資源︰

* [分散式彙總範例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples) (英文)
* [指令碼和 Notebook 範例](https://github.com/Azure/azure-documentdb-spark/tree/master/samples) (英文)

您也可以檢閱 [Apache Spark SQL、DataFrames 和 Datasets 指南](http://spark.apache.org/docs/latest/sql-programming-guide.html) (英文) 和 [Azure HDInsight 上的 Apache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) 文章。



