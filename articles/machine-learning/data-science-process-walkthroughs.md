<properties 
	pageTitle="Team Data Science Process 逐步解說 | Microsoft Azure" 
	description="這些逐步解說示範如何將雲端和內部部署工具與服務組合成工作流程或管線，以建立智慧型應用程式。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2016" 
	ms.author="bradsev" />


# Team Data Science Process 逐步解說

此處分項列出的端對端逐步解說每一個都會示範適用於**特定案例**的 Team Data Science Process 中的步驟，並說明如何將雲端、內部部署工具和服務組合成工作流程或管線，以建立智慧型應用程式。

## 使用 SQL 資料倉儲
[Team Data Science Process 實務：使用 SQL 資料倉儲](machine-learning-data-science-process-sqldw-walkthrough.md)逐步解說會示範如何使用 SQL 資料倉儲 (SQL DW)，針對公開使用的 NYC 計程車車程和車資資料集建置和部署機器學習服務的分類和迴歸模型。

## 使用 SQL Server
[Team Data Science Process 實務：使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md) 逐步解說會示範如何使用 SQL Server 和公開使用的 NYC 計程車車程和車資資料集，建置和部署機器學習服務的分類和迴歸模型。


## 使用 HDInsight Hadoop 叢集
[Team Data Science Process 實務：使用 HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-walkthrough.md)逐步解說會使用 [Azure HDInsight Hadoop 叢集](https://azure.microsoft.com/services/hdinsight/)，對公開使用的 NYC 計程車車程和車資資料集的資料進行儲存、探索和特徵工程設計。


## 在 1 TB 資料集上使用 Azure HDInsight Hadoop 叢集
[Team Data Science Process 實務 - 在 1 TB 資料集上使用 Azure HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-criteo-walkthrough.md)逐步解說會提供一個端對端案例，使用 [Azure HDInsight Hadoop 叢集](https://azure.microsoft.com/services/hdinsight/)進行儲存、探索、特徵工程設計，以及縮減來自其中一個公開使用的 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 資料集的取樣資料。


## 在 Azure 上使用 Python 與 Spark 的資料科學
[在 Azure HDInsight 上使用 Spark 的資料科學](machine-learning-data-science-spark-overview.md)逐步解說會對採用 [Azure HDInsight Spark 叢集](https://azure.microsoft.com/services/hdinsight/)的端對端案例使用 Team Data Science Process，以對公開使用的 NYC 計程車車程和車資資料集的資料進行儲存、探索和特徵工程設計。

## 在 Azure 上使用 Scala 與 Spark 的資料科學
[在 Azure 上使用 Scala 與 Spark 的資料科學](machine-learning-data-science-process-scala-walkthrough.md)說明使用 Scala 搭配 Spark 可調整機器學習程式庫 (MLlib) 和 Azure HDInsight Spark 叢集上的 SparkML 封裝，處理受監督的機器學習工作。它會引導您進行構成[資料科學程序](http://aka.ms/datascienceprocess)的各項工作︰資料擷取和探索、視覺化、特徵設計、模型化和模型取用。建立的模型包括羅吉斯和線性迴歸、隨機樹系和漸層停駐推進式決策樹。


## 使用 Azure Data Lake 儲存體和分析
[Azure Data Lake 中的可調整資料科學︰端對端逐步解說](machine-learning-data-science-process-data-lake-walkthrough.md)示範如何使用 Azure Data Lake，在 NYC 計程車車程和車資資料集範例上執行資料探索和二元分類工作，以預測一趟車程是否收費。

## 搭配使用 R 與 SQL Server R Services
[使用 SQL Server R Services 的資料科學端對端逐步解說](https://msdn.microsoft.com/library/mt612857.aspx)逐步解說會對資料科學家提供 R 程式碼、SQL Server 資料和自訂 SQL 函式的組合，以在 SQL Server 建置和部署 R 模型。

## 搭配使用 T-SQL 與 SQL Server R Services
[適用於 SQL 開發人員的資料庫內進階分析](https://msdn.microsoft.com/library/mt683480.aspx)逐步解說會對 SQL 程式設計人員提供使用 SQL Server R Services 來建置搭配 Transact-SQL 的進階分析方案的實際經驗，以操作 R 方案。

## 後續步驟

如需能引導您完成在 Azure 中構成資料科學程序之工作的主題概觀，請參閱[資料科學程序](http://aka.ms/datascienceprocess)。

<!---HONumber=AcomDC_0803_2016-->