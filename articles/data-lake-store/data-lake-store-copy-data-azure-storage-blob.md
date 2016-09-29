<properties
   pageTitle="將資料從 Azure 儲存體 Blob 複製到資料湖存放區| Microsoft Azure"
   description="使用 AdlCopy 工具將資料從 Azure 儲存體 Blob 複製到資料湖存放區"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/19/2016"
   ms.author="nitinme"/>

# 將資料從 Azure 儲存體 Blob 複製到資料湖存放區

> [AZURE.SELECTOR]
- [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Azure Data Lake Store 提供命令列工具 [AdlCopy](http://aka.ms/downloadadlcopy) 以從下列來源複製資料：

* 從 Azure 儲存體 Blob 複製到 Data Lake Store 中。您無法使用 AdlCopy 將資料從資料湖存放區複製到 Azure 儲存體 Blob。

* 兩個 Azure Data Lake Store 帳戶之間。

此外，您可以兩個不同的模式使用 AdlCopy 工具：

* **單獨使用**，此工具會使用資料湖存放區資源來執行工作。
* **使用資料湖分析帳戶**，指派給資料湖分析帳戶的單位可用來執行複製作業。當您想要以可預測的方式執行複製工作時，可能會想使用此選項。

##必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以使用 Data Lake Store 公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。
- **Azure 儲存體 Blob** 容器 (其中含有一些資料)。
- **Azure 資料湖分析帳戶 (選用)** - 如需如何建立資料湖存放區帳戶的指示，請參閱[開始使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md) 。
- **AdlCopy 工具**。從 [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy) 安裝 AdlCopy 工具。

## AdlCopy 工具的語法

利用下列語法來使用 AdlCopy 工具

	AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

以下將說明語法中的參數：

| 選項 | 說明 |
|-----------|------------|
| 來源 | 指定來源資料在 Azure 儲存體 Blob 中的位置。來源可以是 Blob 容器、Blob 或其他 Data Lake Store 帳戶。 |
| Dest | 指定要複製的資料湖存放區目的地。 |
| SourceKey | 指定 Azure 儲存體 Blob 來源的儲存體存取金鑰。這只有在來源是 Blob 容器或 Blob 時才是必要的。 |
| 帳戶 | **選用**。如果您想要使用 Azure 資料湖分析帳戶來執行複製工作，請使用此選項。如果您在語法中使用 /Account 選項，但未指定資料湖分析帳戶，AdlCopy 就會使用預設帳戶來執行工作。此外，如果您使用此選項，就必須加入來源 (Azure 儲存體 Blob) 和目的地 (Azure 資料湖存放區) 做為資料湖分析帳戶的資料來源。 |
| Units | 指定將針對複製工作使用的資料湖分析單位數目。如果您使用 **/Account** 選項來指定資料湖分析帳戶，則此為必要選項。
| 模式 | 指定用來指示要複製哪些 Blob 或檔案的 regex 模式。AdlCopy 使用區分大小寫的比對。不指定任何模式時所使用的預設模式是複製所有項目。不支援指定多個檔案模式。                                                                                                                                                                                                                                                                                                                                               



## 使用 AdlCopy (獨立) 從 Azure 儲存體 Blob 複製資料

1. 開啟命令提示字元，並瀏覽至安裝 AdlCopy 的目錄，通常是 `%HOMEPATH%\Documents\adlcopy`。

2. 執行下列命令，將特定的 Blob 從來源容器複製到資料湖存放區：

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	例如：

		AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	
	>[AZURE.NOTE] 上述語法指定要複製到 Data Lake Store 帳戶內資料夾中的檔案。如果指定的資料夾名稱不存在，AdlCopy 工具會建立一個資料夾。

	系統會提示您輸入您 Data Lake Store 帳戶所在 Azure 訂用帳戶的認證。您將看到類似以下的輸出：

		Initializing Copy.
		Copy Started.
		100% data copied.
		Finishing Copy.
		Copy Completed. 1 file copied.

3. 您也可以使用下列命令，將所有的 Blob 從某一個容器複製到資料湖存放區帳戶：

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	例如：

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## 使用 AdlCopy (獨立) 從另一個 Data Lake Store 帳戶複製資料

您也可以使用 AdlCopy 在兩個 Data Lake Store 帳戶之間複製資料。

1. 開啟命令提示字元，並瀏覽至安裝 AdlCopy 的目錄，通常是 `%HOMEPATH%\Documents\adlcopy`。

2. 執行下列命令，從 Data Lake Store 帳戶將特定檔案複製到另一個 Data Lake Store 帳戶。

		AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

	例如：

		AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

	>[AZURE.NOTE] 上述語法會指定要複製到目的地 Data Lake Store 帳戶內資料夾中的檔案。如果指定的資料夾名稱不存在，AdlCopy 工具會建立一個資料夾。

	系統會提示您輸入您 Data Lake Store 帳戶所在 Azure 訂用帳戶的認證。您將看到類似以下的輸出：

		Initializing Copy.
		Copy Started.|
		100% data copied.
		Finishing Copy.
		Copy Completed. 1 file copied.

3. 下列命令會將來源 Data Lake Store 帳戶特定資料夾中的所有檔案，複製到目的地 Data Lake Store 帳戶中的資料夾。

		AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## 使用 AdlCopy (利用 Data Lake Analytics 帳戶) 複製資料

您也可以使用資料湖分析帳戶來執行 AdlCopy 工作，將資料從 Azure 儲存體 Blob 複製到資料湖存放區。若要移動的資料大小範圍在 GB 或 TB 內，而且您想要獲得更好且可預期的效能輸送量，您通常會使用此選項。

若要使用您的 Data Lake Analytics 帳戶與 AdlCopy 從 Azure 儲存體 Blob 複製，必須將來源 (Azure 儲存體 Blob) 新增為您 Data Lake Analytics 帳戶的資料來源。如需將其他資料來源加入至資料湖分析帳戶的指示，請參閱[管理資料湖分析帳戶資料來源](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources)。

>[AZURE.NOTE] 如果您是使用 Data Lake Analytics 帳戶從做為來源的 Azure Data Lake Store 帳戶複製，則您不需將 Data Lake Store 帳戶與 Data Lake Analytics 帳戶產生關聯。當來源為 Azure 儲存體帳戶時，才需要將來源儲存體與 Data Lake Analytics 帳戶產生關聯。

執行下列命令，使用 Data Lake Analytics 帳戶從 Azure 儲存體 Blob 複製到 Data Lake Store 帳戶：

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

例如：

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


同樣地，執行下列命令，使用 Data Lake Analytics 帳戶從 Azure 儲存體 Blob 複製到 Data Lake Store 帳戶：

	AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## 使用 AdlCopy 複製使用模式比對的資料

在本節中，您會了解如何使用 AdlCopy 將來源 (在我們使用 Azure 儲存體 Blob 的下列範例中) 的資料複製到使用模式比對的目的地 Data Lake Store 帳戶。例如，您可以使用下列步驟將所有副檔名為 .csv 的檔案從來源 Blob 複製到目的地。

1. 開啟命令提示字元，並瀏覽至安裝 AdlCopy 的目錄，通常是 `%HOMEPATH%\Documents\adlcopy`。

2. 執行下列命令，將所有副檔名為 .csv 的檔案從來源容器的特定 Blob 複製到 Data Lake Store：

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

	例如：

		AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

	
## 計費

* 如果您獨立使用 AdlCopy 工具，而且如果來源 Azure 儲存體帳戶與資料湖存放區位於不同區域，則您將需支付移動資料的輸出成本。

* 如果您將 AdlCopy 工具與資料湖分析帳戶搭配使用，即會套用標準的[資料湖分析計費費率](https://azure.microsoft.com/pricing/details/data-lake-analytics/)。

## 使用 AdlCopy 的考量

* AdlCopy (適用於 1.0.5 版) 支援從共同擁有超過數千個檔案和資料夾的來源複製資料。但是，如果您在複製大型資料集時發生問題，可將檔案/資料夾分散到不同的子資料夾，並改用這些子資料夾的路徑做為來源。

## 後續步驟

- [保護 Data Lake Store 中的資料](data-lake-store-secure-data.md)
- [搭配 Data Lake Store 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->