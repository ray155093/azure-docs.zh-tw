<properties 
	pageTitle="資料轉換︰處理和轉換資料 | Microsoft Azure" 
	description="了解 Azure Data Factory 中的資料轉換。在 Azure HDInsight 叢集或 Azure Batch 中轉換和處理資料。" 
	keywords="資料轉換, 處理資料, 轉換資料, 轉換活動"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# 了解在 Azure Data Factory 中轉換和分析資料
本文說明 Azure Data Factory 中的資料轉換活動，讓您了解 Azure Data Factory 如何將未經處理資料轉換並處理成預測和深入解析。轉換活動會在運算環境中執行，例如 Azure HDInsight 叢集或 Azure Batch。另外也會包含一些示範如何使用特定轉換活動的文章連結。
 
Azure Data Factory 支援下列可個別或與其他活動鏈結而加入至[管線](data-factory-create-pipelines.md)的轉換活動。

資料轉換活動 | 計算環境 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop]
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]
[Hadoop 串流](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[機器學習服務活動：批次執行和更新資源](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[預存程序](data-factory-stored-proc-activity.md) | Azure SQL、Azure SQL 資料倉儲或 SQL Server |
[資料湖分析 U-SQL](data-factory-usql-activity.md) | Azure 資料湖分析 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] 或 Azure Batch
   
> [AZURE.NOTE] 
您可以使用 MapReduce 活動，在 HDInsight Spark 叢集上執行 Spark 程式。如需詳細資訊，請參閱[從 Azure Data Factory 叫用 Spark 程式](data-factory-spark.md)。您可以建立自訂活動，以便在已安裝 R 的 HDInsight 叢集上執行 R 指令碼。請參閱 [使用 Azure Data Factory 執行 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)。
 

您需要為計算環境建立連結服務，然後在定義轉換活動時使用該連結服務。Data Factory 支援兩種類型的資計算環境。

1. **隨選**：在此情況下，運算環境完全由 Data Factory 進行管理。Data Factory 服務會在工作提交前自動建立運算環境以處理資料，而在工作完成時予以移除。您可以針對工作執行、叢集管理及啟動載入動作，設定和控制隨選計算環境的細微設定。
2. **攜帶您自己的裝置**：在此情況下，您可以註冊自己的運算環境 (例如 HDInsight 叢集)，做為 Data Factory 中的連結服務。運算環境由您自行管理，而 Data Factory 會使用它來執行活動。

如需了解 Data Factory 所支援的計算服務，請參閱[計算連結服務](data-factory-compute-linked-services.md)一文。

<!---HONumber=AcomDC_0914_2016-->