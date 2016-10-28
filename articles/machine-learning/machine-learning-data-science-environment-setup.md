<properties 
	pageTitle="設定用於 Team Data Science Process 中的資料科學環境 | Azure" 
	description="設定用於 Team Data Science Process 中的資料科學環境" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/23/2016" 
	ms.author="bradsev" />

# 設定用於 Team Data Science Process 中的資料科學環境

Team Data Science Process 使用各種資料科學環境來儲存、處理和分析資料。他們包含 Azure Blob 儲存體、數種類型的 Azure 虛擬機器、HDInsight (Hadoop) 叢集，以及 Azure Machine Learning 工作區。要使用哪一個環境，取決於要使用機器學習服務進行模型化的資料類型和數量，以及該資料在雲端中的目標目的地。

* 如需進行這項決策時要考量之問題的指引，請參閱[規劃您的 Azure Machine Learning 資料科學環境](machine-learning-data-science-plan-your-environment.md)。
* 如需在進行進階分析時可能會遇到的部分案例目錄，請參閱[適用於 Team Data Science Process 的案例](machine-learning-data-science-plan-sample-scenarios.md)

此功能表所連結的主題會說明如何設定 Team Data Science Process 所用的各種資料科學環境。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

**Microsoft 資料科學虛擬機器**也可做為預先安裝並設定數個常用於資料分析和機器學習之熱門工具的 Azure 虛擬機器 (VM) 映像。請參閱[佈建 Microsoft 資料科學虛擬機器](machine-learning-data-science-provision-vm.md)，以了解如何使用此 VM。

<!---HONumber=AcomDC_0914_2016-->