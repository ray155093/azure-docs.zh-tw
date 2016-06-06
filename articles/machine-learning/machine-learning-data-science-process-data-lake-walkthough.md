<properties
	pageTitle="Azure Data Lake 中可調整的資料科學︰完整的逐步解說 | Microsoft Azure"
	description="如何使用 Azure Data Lake 在資料集上進行資料探索和二元分類工作。"  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,wguo123"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="bradsev;weig"/>


# Azure Data Lake 中的可調整資料科學︰端對端逐步解說

本逐步解說示範如何使用 Azure Data Lake，在 NYC 計程車車程和車費資料集上執行資料探索和二元分類，以預測一趟車程是否收費。其中，從取得資料開始，經過模型訓練，然後部署 Web 服務來發佈模型，從頭到尾逐步引導您完成[資料科學程序](http://aka.ms/datascienceprocess)。

**Azure 資料湖分析**

[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) 具備一切必要的功能，讓資料科學家儲存任何大小、形狀和速度的資料，並以高延展性和符合成本效益的方式，進行資料處理、進階分析和建構機器學習服務模型。只在實際處理資料時，才需要依個別作業付費。Azure Data Lake Analytics 包括 U-SQL，此語言融合 SQL 的宣告性質和 C# 表達能力，提供可調整的分散式查詢功能。它可讓您在讀取、插入自訂邏輯和 UDF 上套用結構描述，以處理非結構化資料，而且包含擴充性，可精細控制如何大規模執行。若要深入了解 U-SQL 背後的設計原理，請參閱 [Visual Studio 部落格文章](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)。

Data Lake Analytics 也是 Cortana Analytics 套件的重要組成部分，可搭配 Azure SQL 資料倉儲、Power BI 與 Data Factory 一起使用。這讓您有一個完整的雲端巨量資料和進階分析平台。

本逐步解說首先描述使用 Data Lake Analytics 完成工作所需的必要條件和資源，以構成資料科學程序，並說明如何安裝這些資源。接著，概述使用 U-SQL 和 Azure Machine Learning 完成此程序的步驟，然後是如何使用 Python 完成類似的工作。


**U-SQL 和 Azure Machine Learning**

本逐步解說使用 Visual Studio 編輯 U-SQL 指令碼來處理資料集。這些 U-SQL 指令碼說明於此，也在個別檔案中提供 。過程包括擷取、探索和取樣資料。接著，顯示如何從 Azure 入口網站執行 U-SQL 指令碼作業。相關聯的 HDInsight 叢集中會為資料建立 Hive 資料表，以利於 Azure Machine Learning Studio 中建置和部署二元分類模型。


**Python**

本逐步解說也有一節說明如何使用 Python，在 NYC 計程車車程和費用資料集樣本上執行這些資料科學工作。針對此程序的每個步驟，我們提供 Jupyter Notebook 和 Python 指令碼。除了此處所述的二元分類模型，此 Notebook 還包含一些額外特徵工程步驟和模型建構的程式碼，例如多類別分類和迴歸模型。迴歸工作是根據其他小費特徵來預測小費金額。


**指令碼**

本逐步解說只概述主要步驟。您可以從 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) 下載完整的 **U-SQL 指令碼**和 **Jupyter Notebook**。


## 必要條件

開始運用這些主題之前，您必須符合下列條件：

- Azure 訂用帳戶。如果還沒有訂用帳戶，請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- [建議] Visual Studio 2013 或 2015。如果您還沒有安裝這些版本的其中一個，您可以從[這裡](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)下載免費的社群版本。按一下 Visual Studio 區段下的 [下載 Community 2015] 按鈕。

>[AZURE.NOTE] 除了 Visual Studio，您也可以使用 Azure 入口網站提交 Azure Data Lake 查詢。我們在**使用 U-SQL 處理資料**一節中提供指示，說明如何使用 Visual Studio 和在入口網站上這樣做。

- 註冊 Azure Data Lake 預覽

>[AZURE.NOTE] Azure Data Lake Store (ADLS) 和 Azure Data Lake Analytics (ADLA) 服務都在預覽階段，您需要經過核准才能使用。當您建立第一個 ADLS 或 ADLA 時會提示您註冊。若要註冊，請按一下 [註冊以預覽]，閱讀合約，然後按一下 [確定]。例如，以下是 ADLS 註冊頁面︰

 ![2](./media/machine-learning-data-science-process-data-lake-walkthough/ADLA-preview-signup.PNG)
 


## 準備 Azure Data Lake 的資料科學環境
若要準備本逐步解說的資料科學環境，請建立下列資源︰

- Azure Data Lake Store (ADLS) 
- Azure Data Lake Analytics (ADLA)
- Azure Blob 儲存體帳戶
- Azure Machine Learning Studio 帳戶
- Azure Data Lake Tools for Visual Studio (建議)

本節提供如何建立這些資源的指示。請注意，Azure Data Lake Store 可以另外建立，也可以在您建立 Azure Data Lake Analytics 時建立為預設儲存體，同樣地，Azure Blob 儲存體帳戶可以另外建立，也可以在您建立 HDInsight Linux 叢集時建立為預設儲存體。以下參考指示來分開建立每一項資源，但這兩個儲存體帳戶不需要分成不同步驟來建立。

### 建立 Azure 資料湖存放區

從 [Azure 入口網站](http://ms.portal.azure.com)建立 ADLS。如需詳細資訊，請參閱[使用 Azure 入口網站建立具有 Data Lake Store 的 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。務必依該文件所述明，在 [選擇性組態] 刀鋒視窗的 [資料來源] 刀鋒視窗中設定 [叢集 AAD 身分識別]。

 ![3](./media/machine-learning-data-science-process-data-lake-walkthough/create_ADLS.PNG)


### 建立 Azure Data Lake Analytics 帳戶
從 [Azure 入口網站](http://ms.portal.azure.com)建立 ADLA 帳戶。如需詳細資訊，請參閱[教學課程：透過 Azure 入口網站開始使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

 ![4](./media/machine-learning-data-science-process-data-lake-walkthough/create_ADLA_new.PNG)


### 建立 Azure Blob 儲存體帳戶
從 [Azure 入口網站](http://ms.portal.azure.com)建立 Azure Blob 儲存體帳戶。如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的「建立儲存體帳戶」一節。
	
 ![5](./media/machine-learning-data-science-process-data-lake-walkthough/Create_Azure_Blob.PNG)


### 設定 Azure Machine Learning Studio 帳戶
從 [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) 頁面註冊/登入 Azure Machine Learning Studio。按一下 [立即開始使用] 按鈕，然後選擇 [免費工作區] 或 [標準工作區]。之後，您就能夠在 Azure ML Studio 中建立實驗。

### 安裝 Azure Data Lake 工具 
依照您的 Visual Studio 版本，從 [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) 安裝適合的 Azure Data Lake 工具。

 ![10](./media/machine-learning-data-science-process-data-lake-walkthough/install_ADL_tools_VS.PNG)

在成功完成安裝之後，開啟 Visual Studio。您在頂端的功能表應該會看到 Data Lake 索引標籤。當您登入 Azure 帳戶時，您的 Azure 資源應該會出現在左面板中。

 ![11](./media/machine-learning-data-science-process-data-lake-walkthough/install_ADL_tools_VS_done.PNG)


## NYC 計程車車程資料集
我們在此處使用的資源集是公開可用的資料集 -- [NYC 計程車車程](http://www.andresmh.com/nyctaxitrips/)資料集。「NYC 計程車車程」資料是由約 20GB 的 CSV 壓縮檔 (未壓縮時可達 48GB) 所組成，裡面記錄了超過 1 億 7300 萬筆個別車程及針對每趟車程所支付的費用。每趟車程記錄包括上車和下車的位置與時間、匿名的計程車司機駕照號碼，以及計程車牌照 (計程車的唯一識別碼) 號碼。資料涵蓋 2013 年的所有車程，並且每月會在下列兩個資料集中加以提供：

 - 「trip\_data」CSV 檔案包含車程的詳細資訊，例如，乘客數、上車和下車地點、車程持續時間，以及車程長度。以下是一些範例記錄：

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - 「trip\_fare」CSV 檔案包含針對每趟車程所支付之費用的詳細資訊，例如付款類型、費用金額、銷售稅和稅金、小費和服務費，以及支付的總金額。以下是一些範例記錄：

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

聯結 trip\_data 和 trip\_fare 的唯一索引鍵是由下列三個欄位所組成：medallion、hack\_license 和 pickup\_datetime。從公用 Azure 儲存體 blob 中可以存取原始 CSV 檔案。

## 使用 U-SQL 處理資料

本節所述的資料處理工作包括擷取、檢查品質、探索和取樣資料。我們也說明如何聯結車程和費用資料表。最後一節說明從 Azure 入口網站執行 U-SQL 指令碼作業。以下是各小節的連結︰

- [資料擷取：從公用 blob 讀取資料](#ingest)
- [資料品質檢查](#quality)
- [資料探索](#explore)
- [如何聯結車程和費用資料表](#join)
- [資料取樣](#sample)
- [執行 U-SQL 作業](#run)

這些 U-SQL 指令碼說明於此，也在個別檔案中提供 。您可以從 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) 下載完整的 **U-SQL 指令碼**。

若要執行 U-SQL，請開啟 Visual Studio，按一下 [檔案--> [新增]--> [專案]，選擇 [U-SQL 專案]，命名並儲存到資料夾。

![12](./media/machine-learning-data-science-process-data-lake-walkthough/create_USQL_project.PNG)

>[AZURE.NOTE] 如果使用 Azure 入口網站執行 U-SQL，而不是使用 Visual Studio，您可以瀏覽至 Azure Data Lake Analytics 資源，然後依照這些說明步驟來提交查詢。


![29](./media/machine-learning-data-science-process-data-lake-walkthough/portal_submit_job.PNG)

### <a name="ingest"></a>資料擷取：從公用 blob 讀取資料

Azure blob 中的資料位置是以 ****wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** 來參考，可透過 **Extractors.Csv()** 來擷取。在下列指令碼中，以您自己的容器名稱和儲存體帳戶名稱替換 wasb 位址中的 container_name@blob_storage_account_name。由於檔案名稱是相同格式，我們可以使用 **trip\_data\_{*}.csv** 讀取全部 12 個車程檔案。

	///Read in Trip data
	@trip0 =
	    EXTRACT 
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

由於第一列有標頭，我們必須移除標頭，並將資料行類型變更為適當的類型。我們可以使用 ****swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_，將已處理的資料儲存至 Azure Data Lake Storage，或使用 ****wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**，儲存至 Azure Blob 儲存體帳戶。

	// change data types
	@trip =
	    SELECT 
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    DateTime.Parse(pickup_datetime) AS pickup_datetime,
	    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
	    Int32.Parse(passenger_count) AS passenger_count,
	    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
	    Double.Parse(trip_distance) AS trip_distance,
	    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
	    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
	    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
	    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

	////output data to ADL
	OUTPUT @trip   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_trip.csv"
	USING Outputters.Csv(); 

	////Output data to blob
	OUTPUT @trip   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
	USING Outputters.Csv();  

同樣地，我們可以讀取費用資料集。以滑鼠右鍵按一下 Azure Data Lake Store，您可以選擇在 [Azure 入口網站--> 資料總管] 或 Visual Studio 內的 [檔案總管] 中查看您的資料。

 ![13](./media/machine-learning-data-science-process-data-lake-walkthough/data_in_ADL_VS.PNG)

 ![14](./media/machine-learning-data-science-process-data-lake-walkthough/data_in_ADL.PNG)


### <a name="quality"></a>資料品質檢查

讀取車程和費用資料表之後，即可依下列方式檢查資料品質。產生的 CSV 檔案可以輸出至 Azure Blob 儲存體或 Azure Data Lake Store。

尋找獎章數目和卓越的獎章數目：

	///check the number of medallions and unique number of medallions
	@trip2 =
	    SELECT
	    medallion,
	    vendor_id,
	    pickup_datetime.Month AS pickup_month
	    FROM @trip;
	
	@ex_1 =
	    SELECT
	    pickup_month, 
	    COUNT(medallion) AS cnt_medallion,
	    COUNT(DISTINCT(medallion)) AS unique_medallion
	    FROM @trip2
	    GROUP BY pickup_month;
	    OUTPUT @ex_1   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
	USING Outputters.Csv(); 

尋找有超過 100 個車程的牌照：

	///find those medallions that had more than 100 trips
	@ex_2 =
	    SELECT medallion,
	           COUNT(medallion) AS cnt_medallion
	    FROM @trip2
	    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
	    GROUP BY medallion
	    HAVING COUNT(medallion) > 100;
	    OUTPUT @ex_2   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
	USING Outputters.Csv(); 

找到 pickup\_longitude 方面的無效記錄︰

	///find those invalid records in terms of pickup_longitude
	@ex_3 =
	    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
	    FROM @trip
	    WHERE
	    pickup_longitude <- 90 OR pickup_longitude > 90;
	    OUTPUT @ex_3   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
	USING Outputters.Csv(); 

尋找一些變數的遺漏值︰

	//check missing values
	@res =
	    SELECT *,
	           (medallion == null? 1 : 0) AS missing_medallion
	    FROM @trip;
	
	@trip_summary6 =
	    SELECT 
	        vendor_id,
	    SUM(missing_medallion) AS medallion_empty, 
	    COUNT(medallion) AS medallion_total,
	    COUNT(DISTINCT(medallion)) AS medallion_total_unique  
	    FROM @res
	    GROUP BY vendor_id;
	OUTPUT @trip_summary6
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
	USING Outputters.Csv();



### <a name="explore"></a>資料探索

我們稍微探索資料以更充分了解資料。

尋找有小費和無小費車程的分佈︰

	///tipped vs. not tipped distribution
	@tip_or_not =
	    SELECT *,
	           (tip_amount > 0 ? 1: 0) AS tipped
	    FROM @fare;
	
	@ex_4 =
	    SELECT tipped,
	           COUNT(*) AS tip_freq
	    FROM @tip_or_not
	    GROUP BY tipped;
	    OUTPUT @ex_4   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
	USING Outputters.Csv(); 

尋找有截止值的小費金額分佈︰0、5、10 和 20 美金。

	//tip class/range distribution
	@tip_class =
	    SELECT *,
	           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
	    FROM @fare;
	@ex_5 =
	    SELECT tip_class,
	           COUNT(*) AS tip_freq
	    FROM @tip_class
	    GROUP BY tip_class;
	    OUTPUT @ex_5   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
	USING Outputters.Csv(); 

尋找車程距離的基本統計資料︰

	// find basic statistics for trip_distance
	@trip_summary4 =
	    SELECT 
	        vendor_id,
	        COUNT(*) AS cnt_row,
	        MIN(trip_distance) AS min_trip_distance,
	        MAX(trip_distance) AS max_trip_distance,
	        AVG(trip_distance) AS avg_trip_distance 
	    FROM @trip
	    GROUP BY vendor_id;
	OUTPUT @trip_summary4
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
	USING Outputters.Csv();

尋找車程距離的百分位數︰

	// find percentiles of trip_distance
	@trip_summary3 =
	    SELECT DISTINCT vendor_id AS vendor,
	                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
	    FROM @trip;
	   // group by vendor_id;
	OUTPUT @trip_summary3
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
	USING Outputters.Csv(); 


### <a name="join"></a>聯結車程和費用資料表

車程和費用資料表可以由 medallion、hack\_license 和 pickup\_time 聯結。

	//join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

	//// output to blob
	OUTPUT @model_data_full   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 

	////output data to ADL
	OUTPUT @model_data_full   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 


對於每個層級的乘客計數，計算記錄數、平均小費金額、小費金額變異數、小費車程的百分比。

	// contigency table
	@trip_summary8 =
	    SELECT passenger_count,
	           COUNT(*) AS cnt,
	           AVG(tip_amount) AS avg_tip_amount,
	           VAR(tip_amount) AS var_tip_amount,
	           SUM(tipped) AS cnt_tipped,
	           (float)SUM(tipped)/COUNT(*) AS pct_tipped
	    FROM @model_data_full
	    GROUP BY passenger_count;
	    OUTPUT @trip_summary8
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
	USING Outputters.Csv();


### <a name="sample"></a>資料取樣

首先，我們從聯結的資料表隨機選取 0.1% 的資料︰

	//random select 1/1000 data for modeling purpose
	@addrownumberres_randomsample =
	SELECT *,
	        ROW_NUMBER() OVER() AS rownum
	FROM @model_data_full;
	
	@model_data_random_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_randomsample
	WHERE rownum % 1000 == 0;
	
	OUTPUT @model_data_random_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
	USING Outputters.Csv(); 

接著，我們以二元變數 tip\_class 進行分層取樣：

	//stratified random select 1/1000 data for modeling purpose
	@addrownumberres_stratifiedsample =
	SELECT *,
	        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
	FROM @model_data_full;
	
	@model_data_stratified_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_stratifiedsample
	WHERE rownum % 1000 == 0;
	//// output to blob
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 
	////output data to ADL
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 


### <a name="run"></a>執行 U-SQL 作業

完成編輯 U-SQL 指令碼時，您可以使用 Azure Data Lake Analytics 帳戶將它們提交到伺服器。按一下 [Data Lake]、[提交作業]、選取您的 [Analytics 帳戶]、選擇 [平行處理原則]，然後按一下 [提交] 按鈕。

 ![15](./media/machine-learning-data-science-process-data-lake-walkthough/submit_USQL.PNG)

成功編譯作業後，您作業的狀態會顯示在 Visual Studio 中供您監視。作業執行完成後，您甚至可以重播作業執行程序，找出瓶頸步驟來提升作業效率。您也可以移至 Azure 入口網站來檢查 U-SQL 作業的狀態。

 ![16](./media/machine-learning-data-science-process-data-lake-walkthough/USQL_running_v2.PNG)


 ![17](./media/machine-learning-data-science-process-data-lake-walkthough/USQL_jobs_portal.PNG)


現在，您可以在 Azure Blob 儲存體或 Azure 入口網站中檢查輸出檔案。在下一個步驟中，我們將在模型中使用分層取樣資料。

 ![18](./media/machine-learning-data-science-process-data-lake-walkthough/U-SQL-output-csv.PNG)

 ![19](./media/machine-learning-data-science-process-data-lake-walkthough/U-SQL-output-csv-portal.PNG)

## 在 Azure Machine Learning 中建置和部署模型
我們將示範兩個選項，讓您將資料提取到 Azure Machine Learning。在第一個選項中，您可以使用寫入 Azure Blob 的取樣資料 (來自上述的**資料取樣**步驟)，再使用 Python 建立模型並部署到 Azure Machine Learning。在第二個選項中，您可以直接使用 Hive 查詢來查詢 Azure Data Lake 中的資料。此選項將需要建立新的 HDInsight 叢集，或使用現有的 HDInsight 叢集，其中 Hive 資料表指向 Azure Data Lake Storage 中的資料。以下我們討論這兩個選項。

### 選項 1︰使用 Python 建置和部署機器學習服務模型

現在讓我們使用 Python 來建置和部署機器學習服務模型。我們可以使用 Python 從 Azure Blob 儲存體讀取已處理的資料 (在上述**資料取樣**步驟中以 U-SQL 儲存)，並建立模型。在本機電腦或 Azure Machine Learning Studio 中建立 Jupyter Notebook。本文中，我們只示範模型化和部署步驟。Jupyter Notebook 包含完整的程式碼，可探索、視覺化資料、特徵工程、模型化和部署。

#### 匯入 Python 程式庫

若要執行範例 Jupyter Notebook 或 Python 指令碼檔案，您需要下列 Python 封裝。如果您使用 AzureML Notebook 服務，則已預先安裝這些封裝。

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage.blob import BlobService
	import tables
	import time
	import zipfile
	import random
	import sklearn
	from sklearn.linear_model import LogisticRegression
	from sklearn.cross_validation import train_test_split
	from sklearn import metrics
	from __future__ import division
	from sklearn import linear_model
	from azureml import services


#### 從 blob 讀取資料

- 連接字串   

		CONTAINERNAME = 'test1'
		STORAGEACCOUNTNAME = 'XXXXXXXXX'
		STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
		BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
		blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	
- 以文字形式讀取

		t1 = time.time()
		data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
		t2 = time.time()
		print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![29](./media/machine-learning-data-science-process-data-lake-walkthough/python_readin_csv.PNG)
 
- 加入資料行名稱和分隔資料行

		colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
		'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
		df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
	


- 將某些資料行變更為數值

		cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
		,'tipped','tip_class','rownum']
		for col in cols_2_float:
		    df1[col] = df1[col].astype(float)

#### 建置機器學習服務模型

在此，我們建置二元分類模型，預測一趟車程是否收到小費。在 Jupyter Notebook 中，您可以發現其他兩個模型︰多類別分類和迴歸模型。

- 首先，我們需要建立可在 scikit-learn 模型中使用的虛擬變數

		df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
		df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- 建立模型化的資料框架

		cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
		data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
		
		X = data.iloc[:,1:]
		Y = data.tipped

- 訓練和測試 60-40 分割

		X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- 訓練集中的羅吉斯迴歸

		model = LogisticRegression()
		logit_fit = model.fit(X_train, Y_train)
		print ('Coefficients: \n', logit_fit.coef_)
		Y_train_pred = logit_fit.predict(X_train)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/py_logit_coefficient.PNG)

- 評分測試資料集

		Y_test_pred = logit_fit.predict(X_test)

- 計算評估度量

		fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
		print fpr_train, tpr_train, thresholds_train
		
		fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
		print fpr_test, tpr_test, thresholds_test
		
		#AUC
		print metrics.auc(fpr_train,tpr_train)
		print metrics.auc(fpr_test,tpr_test)
		
		#Confusion Matrix
		print metrics.confusion_matrix(Y_train,Y_train_pred)
		print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/py_logit_evaluation.PNG)


 
#### 建置 Web 服務 API 並在 Python 中使用

我們希望機器學習服務模型在建置後開始運作。在此，我們以二元羅吉斯模型為例子。請確定本機電腦中的 scikit-learn 版本是 0.15.1。如果您使用 Azure ML Studio 服務，則不必擔心這一點。

- 從 Azure ML Studio 設定中尋找您的工作區認證。在 Azure Machine Learning Studio 中，按一下 [設定] --> [名稱] --> [授權權杖]。 

	![](./media/machine-learning-data-science-process-data-lake-walkthough/workspace_id.PNG)


		workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
		auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- 建立 Web 服務

		@services.publish(workspaceid, auth_token) 
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(int) #0, or 1
		def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
		    return logit_fit.predict(inputArray)

- 取得 Web 服務認證

		url = predictNYCTAXI.service.url
		api_key =  predictNYCTAXI.service.api_key
		
		print url
		print api_key

		@services.service(url, api_key)
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(float)
		def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    pass

- 呼叫 Web 服務 API。執行上一個步驟後，您必須等候 5-10 秒。

		NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/call_API.PNG)

### 選項 2：直接在 Azure Machine Learning 中建立和部署模型

您可以使用 Azure Machine Learning Studio 直接從 Azure Data Lake Store 讀取資料、建立模型，然後部署部署。為了讓 Azure Machine Learning 直接從 Azure Data Lake Store 讀取資料，您需要建立 Hive 資料表。因此，需要佈建不同的 Azure HDInsight 叢集，並於此叢集上建立 Hive 資料表來指向 Azure Data Lake Store。下列小節示範如何這樣做。

#### 建立 HDInsight Linux 叢集
從 [Azure 入口網站](http://ms.portal.azure.com)建立 HDInsight 叢集 (Linux)。如需詳細資訊，請參閱[使用 Azure 入口網站建立具有 Data Lake Store 的 HDInsight 叢集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)中的「建立可存取 Azure Data Lake Store 的 HDInsight 叢集」一節。

 ![6](./media/machine-learning-data-science-process-data-lake-walkthough/create_HDI_cluster.PNG)

#### 在 HDInsight 中建立 Hive 資料表

現在，我們要使用上一個步驟中儲存在 Azure Data Lake Store 中的資料，在 HDInsight 叢集中建立 Hive 資料表。在下一個步驟中，Azure Machine Learning Studio 中會使用這些 Hive 資料表。請移至本逐步解說開頭所建立的 HDInsight 叢集。按一下 [設定] --> [屬性] --> [叢集 AAD 身分識別] --> [ADLS 存取]，確定您的 Azure Data Lake Store 帳戶已加入清單中，且具有讀取、寫入及執行權限。

 ![20](./media/machine-learning-data-science-process-data-lake-walkthough/HDI_cluster_add_ADLS.PNG)


然後，按一下 [設定] 按鈕旁邊的 [儀表板]，隨即出現一個視窗。按一下頁面右上角的 [Hive 檢視]，您將會看到 [查詢編輯器]。

 ![21](./media/machine-learning-data-science-process-data-lake-walkthough/HDI_dashboard.PNG)

 ![22](./media/machine-learning-data-science-process-data-lake-walkthough/Hive_Query_Editor_v2.PNG)


貼上下列 Hive 指令碼來建立資料表。Azure Data Lake Store 中以這種方式參考資料來源的位置：****adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**。

	CREATE EXTERNAL TABLE nyc_stratified_sample
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string,
	  payment_type string,
	  fare_amount string,
	  surcharge string,
	  mta_tax string,
	  tolls_amount string,
	  total_amount string,
	  tip_amount string,
	  tipped string,
	  tip_class string,
	  rownum string
	  )
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	LOCATION 'adl://cdsp.azuredatalakestore.net:443/nyctaxi_weig/demo_ex_9_stratified_1_1000_copy.csv';

當查詢完成執行時，您會看到像這樣的結果︰

 ![23](./media/machine-learning-data-science-process-data-lake-walkthough/Hive_Query_results.PNG)



#### 在 Azure Machine Learning Studio 中建置和部署模型

我們現在已準備好繼續在 Azure Machine Learning 中進行模型建置和模型部署。資料已可用於以取樣資料進行的預測問題上︰二元分類 (是否給小費)、多類別分類 (tip\_class) 和迴歸 (tip\_amount)。在此，我們將說明如何在 Azure Machine Learning Studio 中建置和部署二元分類模型。

1. 利用 [資料輸入和輸出] 區段中提供的 **Reader** 模組，將資料匯入 Azure ML。如需詳細資訊，請參閱 [Reader 模組](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)參考頁面。
2. 在 [屬性] 面板中，選取 [Hive 查詢] 做為 [資料來源]。
3. 將下列 Hive 指令碼貼到 [Hive 資料庫查詢] 編輯器中

    	select * from nyc_stratified_sample;

4. 輸入 HDInsight 叢集的 URI (這可以在 Azure 入口網站中找到)、Hadoop 認證、輸出資料的位置，以及 Azure 儲存體帳戶名稱/金鑰/容器名稱。

 ![24](./media/machine-learning-data-science-process-data-lake-walkthough/reader_module_v3.PNG)

下圖顯示從 Hive 資料表讀取資料的二元分類實驗範例。

 ![25](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp.PNG)

建立實驗之後，按一下 [設定 Web 服務] --> [預測性 Web 服務]

 ![26](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp_deploy.PNG)

執行自動建立的評分實驗，完成後，按一下 [部署 Web 服務]

 ![27](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp_deploy_web.PNG)

不久後就會 Web 服務儀表板︰

 ![28](./media/machine-learning-data-science-process-data-lake-walkthough/AML_web_api.PNG)


## 摘要

回顧我們在本逐步解說中完成的作業︰

- 建立資料科學環境，以便在 Azure Data Lake 中建置可調整的端對端解決方案；
- 透過資料科學程序的標準步驟，使用 U-SQL 和 Python 取得大型的公用資料集，過程中從取得資料開始、經過模型訓練，最後將模型部署成 Web 服務。

<!---HONumber=AcomDC_0525_2016-->