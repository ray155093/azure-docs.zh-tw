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
	ms.date="06/14/2016" 
	ms.author="bradsev" />


# Team Data Science Process 逐步解說

此處分項列出的端對端逐步解說每一個都會示範適用於**特定案例**的 Team Data Science Process 中的步驟，並說明如何將雲端和內部部署工具和服務組合成工作流程或管線，以建立智慧型應用程式。

## 使用 SQL Server
在 [Team Data Science Process 實務：使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md) 逐步解說示範如何使用 SQL Server 和公開可用的資料集 ([NYC Taxi Trips (NYC 計程車車程)](http://www.andresmh.com/nyctaxitrips/) 資料集) 來建置和部署機器學習模型


## 使用 HDInsight Hadoop 叢集
在 [Team Data Science Process 實務︰使用 HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-walkthrough.md)逐步解說中，我們使用 [Azure HDInsight Hadoop 叢集](https://azure.microsoft.com/services/hdinsight/)，對 [NYC Taxi Trips (NYC 計程車車程)](http://www.andresmh.com/nyctaxitrips/) 資料集內公開可用的資料進行儲存、探索和特徵工程設計。


## Team Data Science Process 實務 - 在 1 TB 資料集上使用 Azure HDInsight Hadoop 叢集
在 [Team Data Science Process 實務 - 在 1 TB 資料集上使用 Azure HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-criteo-walkthrough.md)逐步解說中，我們會提供一個端對端案例，使用 [Azure HDInsight Hadoop 叢集](https://azure.microsoft.com/services/hdinsight/)進行儲存、探索、特徵工程設計，以及縮減來自其中一個公開可用的 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 資料集的取樣資料。

## 在 Azure HDInsight 上使用 Spark 的資料科學
於[在 Azure HDInsight 上使用 Spark 的資料科學](machine-learning-data-science-spark-overview.md)逐步解說中，我們會對採用 [Azure HDInsight Spark 叢集](https://azure.microsoft.com/services/hdinsight/)的端對端案例使用 Team Data Science Process，以對 [NYC Taxi Trips (NYC 計程車車程)](http://www.andresmh.com/nyctaxitrips/) 資料集內可公開使用的資料進行儲存、探索和特徵工程設計。


## Azure Data Lake 中的可調整資料科學
[Azure Data Lake 中的可調整資料科學︰端對端逐步解說](machine-learning-data-science-process-data-lake-walkthrough.md)示範如何使用 Azure Data Lake，在 NYC 計程車車程和車費資料集範例上執行資料探索和二元分類工作，以預測一趟車程是否收費。

## 使用 SQL Server R Services 的資料科學端對端逐步解說
[使用 SQL Server R Services 的資料科學端對端逐步解說](https://msdn.microsoft.com/library/mt612857.aspx)使用已知的公用資料集 (紐約市計程車資料集)。您可以使用 R 程式碼、SQL Server 資料和自訂的 SQL 函數的組合來建立分類模型，以指出司機將在特定計程車車程中得到小費的機率。您也會將 R 模型部署到 SQL Server，並根據該模型使用伺服器資料來產生分數。

## 適用於 SQL 開發人員的資料庫內進階分析

[適用於 SQL 開發人員的資料庫內進階分析](https://msdn.microsoft.com/library/mt683480.aspx)逐步解說的目標，是使用 SQL Server R Services 來為 SQL 程式設計人員提供建置進階分析方案的實際經驗。您已了解如何藉由將 R 程式碼包裝在預存程序中，來將 R 整合到應用程式或 BI 方案。

<!---HONumber=AcomDC_0622_2016-->