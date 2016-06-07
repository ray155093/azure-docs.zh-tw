<properties
   	pageTitle="發佈 HDInsight 應用程式 | Microsoft Azure"
   	description="了解如何建立和發佈 HDInsight 應用程式。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="06/01/2016"
   	ms.author="jgao"/>

# 將 HDInsight 應用程式發佈到 Azure Marketplace

HDInsight 應用程式是使用者可以在以 Linux 為基礎的 HDInsight 叢集上安裝的應用程式。Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。在本文中，您將學習如何將 HDInsight 應用程式發佈到 Azure Marketplace。如需發佈到 Azure Marketplace 的一般資訊，請參閱[將提供項目發佈到 Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md)。

HDInsight 應用程式採用「自備授權 (BYOL)」 模型，其中的應用程式提供者負責將應用程式授權給一般使用者，而 Azure 只會向一般使用者收取其所建資源的費用，例如 HDInsight 叢集與其 VM/節點。此時，Azure 不經手應用程式本身的計費。

其他 HDInsight 應用程式相關文章︰

- [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何安裝和測試自訂 HDInsight 應用程式。

 
## 必要條件

若要將自訂應用程式提交至 Marketplace，您必須建立並測試您的自訂應用程式。請參閱下列文章：

- [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何安裝和測試自訂 HDInsight 應用程式。

您還必須註冊開發人員帳戶。請參閱[將提供項目發佈到 Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) 和[建立 Microsoft 開發人員帳戶](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。

## 定義應用程式

將應用程式發佈至 Azure Marketplace 涉及兩個步驟。首先，定義 **createUiDef.json** 檔，以指出與您的應用程式相容的叢集，然後從 Azure 入口網站發佈範本。以下是範例 createUiDef.json 檔案。

	{
		"handler": "Microsoft.HDInsight",
		"version": "0.0.1-preview",
		"clusterFilters": {
			"types": ["Hadoop", "HBase", "Storm", "Spark"],
			"tiers": ["Standard", "Premium"],
			"versions": ["3.4"]
		}
	}


|欄位 | 說明 | 可能的值|
|-------|---------------|----------------|
|types |與應用程式相容的叢集類型。 |Hadoop、HBase、Storm、Spark (或這些類型的任意組合)|
|tiers |與應用程式相容的叢集層。 |Standard、Premium (或兩者)|
|versions|	與應用程式相容的 HDInsight 叢集類型。 |3\.4|

## 封裝應用程式

建立 zip 檔案，其中包含安裝 HDInsight 應用程式時的所有必要檔案。您需要[發佈應用程式](#publish-application)中的 zip 檔案。

- [createUiDefinition.json](#define-application)。
- mainTemplate.json。請參閱[安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)中的範例。

	>[AZURE.IMPORTANT] 應用程式安裝指令碼的名稱必須是特定叢集中唯一的名稱 (採用以下的格式)。
	
	>	name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
		
	>請注意，指令碼名稱有三個部分︰
		
	>	1. A script name prefix, which shall include either the application name or a name relevant to the application.
	>	2. A "-" for readability.
	>	3. A unique string function with the application name as the parameter.

	>	An example is the above ends up becoming: hue-install-v0-4wkahss55hlas in the persisted script action list. For a sample JSON payload, see [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- 所有必要的指令碼。

> [AZURE.NOTE] 應用程式檔案 (包括 Web 應用程式檔案 (若有的話)) 可以位於任何可公開存取的端點上。

## 發佈應用程式

請遵循下列步驟來發佈 HDInsight 應用程式︰

1. 登入 [Azure 發佈入口網站](https://publish.windowsazure.com/)。
2. 按一下 [方案範本] 來建立新的方案範本。
3. 按一下 [建立開發人員中心帳戶並加入 Azure 方案] 以註冊您的公司 (如果尚未這麼做)。請參閱[建立 Microsoft 開發人員帳戶](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。
4. 按一下 [定義一些拓撲以便開始使用]。解決方案範本是所有其拓撲的「父項」。您可以在一個供應項目/解決方案範本中定義多個拓撲。當供應項目進入預備環境時，它的所有拓撲也會一起進入。 
5. 加入新的版本。
6. 上傳在[封裝應用程式](#package-application)中準備的 zip 檔案。  
7. 按一下 [要求認證]。Microsoft 認證團隊會檢閱檔案並認證拓撲。

## 後續步驟

- [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何將未發佈的 HDInsight 應用程式部署到 HDInsight。
- [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
- [使用 ARM 範本在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 ARM 範本來建立 HDInsight 叢集。

<!---HONumber=AcomDC_0601_2016-->