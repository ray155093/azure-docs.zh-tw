<properties
	pageTitle="在 Azure HDInsight 上使用 Spark 的資料科學概觀 | Microsoft Azure"
	description="Spark MLlib 工具組將可觀的機器學習模型化功能引進分散式 HDInsight 環境中。"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="bradsev" />

# 在 Azure HDInsight 上使用 Spark 的資料科學概觀

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

## 簡介

Azure HDInsight 上的 Spark 能夠管理記憶體中的資料，同時以分散式方式在 HDInsight (Hadoop) 叢集上處理它。因此，此 Spark 叢集結合了速度與容量。它也支援 Spark 叢集上的 Jupyter Notebook，可執行 Spark SQL 互動式查詢以轉換、篩選和視覺化 Azure Blob (WASB) 中儲存的資料。

[Spark](http://spark.apache.org/) 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。Spark 處理引擎是專為速度、易用性及精密分析打造的產品。Spark 的記憶體內計算功能，使其成為機器學習和圖表計算中反覆演算法的絕佳選擇 。[MLlib](http://spark.apache.org/mllib/) 是 Spark 的可調整機器學習庫。

HDInsight Spark 是開放原始碼 Spark 的 Azure 託管服務。此逐步解說所提供的設定步驟和程式碼適用於 HDInsight Spark。不過，程式碼屬泛型，而且應該在任何 Spark 叢集上運作。若未使用 HDInsight Spark，叢集設定和管理步驟可能與這裡顯示的稍有不同。

Spark MLlib 工具組針對此分散式環境帶來相當大的機器學習 (ML) 模型化功能。功能表中連結的主題集合顯示此狀況，方法是在 NYC 計程車車程和費用 2013 資料集中執行二進位分類和迴歸工作，然後將模型結果儲存於 WASB。模型建置包含羅吉斯和線性迴歸、隨機樹系和漸層停駐推進式決策樹。它們也顯示如何使用這些模型的結果，針對 WASB 儲存的其他資料集進行評分和評估。更進階的主題將討論如何使用交叉驗證和超參數牽涉來訓練模型。

這些主題中的模型化步驟加入程式碼來示範如何訓練、評估、儲存和使用每類模型。已使用 Python 來編碼解決方案並顯示相關的繪圖，並已提供 Python Notebook，以便在 Spark 叢集上安裝的 Jupyter Notebook 中執行。


## 必要條件

1\. 開始這些主題之前，您必須擁有 Azure 訂用帳戶。如果還沒有，請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

2\. 若要建立 HDInsight Spark 叢集，請參閱[開始使用：在 Azure HDInsight 上建立 Apache Spark](../hdinsight/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)。


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## NYC 2013 計程車資料

「NYC 計程車車程」資料大約是 20GB 以逗號分隔值 (CSV) 的壓縮檔 (未壓縮時可達 48GB)，其中包含超過 1 億 7300 萬筆個別車程及針對每趟車程支付的費用。每趟車程記錄包括上車和下車的位置與時間、匿名的計程車司機駕照號碼，以及圓形徽章 (計程車的唯一識別碼) 號碼。資料涵蓋 2013 年的所有車程，並且每月會在下列兩個資料集中加以提供：

1. 'trip\_data' CSV 檔案包含車程詳細資訊，例如乘客數、上車和下車地點、車程持續時間，以及車程長度。以下是一些範例記錄：

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip\_data' CSV 檔案包含針對每趟車程所支付的費用詳細資料，例如付款類型、費用金額、附加費和稅金、小費和通行費，以及支付的總金額。以下是一些範例記錄：

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


聯結 trip\\_data and trip\\_fare 的唯一索引鍵是由下列欄位組成：medallion、hack\\_licence 和 pickup\\_datetime。


我們已取得這些檔案的 0.1% 樣本，並將其聯結成單一資料集，做為此逐步解說的輸入資料集。聯結 trip\\_data and trip\\_fare 的唯一索引鍵是由下列欄位組成：medallion、hack\\_licence 和 pickup\\_datetime。資料集的每一筆記錄會包含代表 NYC 計程車車程的下列屬性︰


|欄位| 簡短描述
|------|---------------------------------
| medallion |匿名的計程車圓形徽章 (唯一的計程車識別碼)
| hack\_license |	匿名的 Hackney 歸位字元駕照號碼
| vendor\_id |	計程車廠商識別碼
| rate\_code | NYC 計程車費率
| store\_and\_fwd\_flag | 儲存和轉寄旗標
| pickup\_datetime |	上車日期和時間
| dropoff\_datetime | 下車日期和時間
| pickup\_hour |	上車小時
| pickup\_week |	上車年中星期
| weekday |	工作日 (範圍 1-7)
| passenger\_count |	計程車車程的乘客數目
| trip\_time\_in\_secs | 往返時間 (秒)
| trip\_distance | 以英哩計的車程距離
| pickup\_longitude | 上車經度
| pickup\_latitude |	上車緯度
| dropoff\_longitude | 下車經度
| dropoff\_latitude | 下車緯度
| direct\_distance |	上車與下車位置之間的直線距離
| payment\_type | 付款類型 (cas、信用卡等)
| fare\_amount | 費用金額
| surcharge | 銷售稅
| mta\_tax |	Mta 稅額
| tip\_amount | 小費金額
| tolls\_amount | 收費金額
| total\_amount | 總金額
| tipped | 已收到小費 (0/1 表示否或是)
| tip\_class | 小費類別 (0：$0、1：$0-5、2：$6-10、3：$11-20、4：> $20)


## 從 Spark 叢集的 Jupyter Notebook 中執行程式碼 

您可以從 Azure 入口網站啟動 Jupyter Notebook：尋找 Spark 叢集，並按一下它以輸入詳細的叢集管理頁面。您可以在那裡按一下 [叢集儀表板] 中 Spark 叢集關聯的 Jupyter Notebook 圖示。

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

您也可以以瀏覽至 ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** 來存取 Jupyter Notebook。您需要有系統管理員帳戶的密碼才能存取 Notebook。

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

瀏覽至 Python，以查看執行 python 指令碼的現有 Notebook。您會看到一個目錄，其中包含一些預先封裝的 Notebook 範例。可在 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python) 中取得包含本主題中程式碼範例的 Notebook。

您可以將 Notebook 直接從 Github 上傳至 Spark 叢集上的 Jupyter Notebook 伺服器。在您的 Jupyter 首頁，按一下螢幕右側的 [上傳] 按鈕。它會開啟檔案總管。您可以在這裡貼上 Notebook 的 Github (原始內容) URL，然後按一下 [開啟]。您會在 Jupyter 檔案清單中看到檔案名稱，並重新出現 [上傳] 按鈕。按一下此 [上傳] 按鈕。現在您已匯入 Notebook。重複這些步驟，從此逐步解說上傳下列 Notebook。

1.	machine-learning-data-science-spark-data-exploration-modeling.ipynb
2.	machine-learning-data-science-spark-model-consumption.ipynb
3.	machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb

現在您可以：

- 按一下 Notebook 以查看程式碼
- 按 **SHIFT-ENTER** 執行每個儲存格
- 按一下 [儲存格] -> [執行] 執行整個 Notebook


## 後續步驟

現在您已使用 HDInsight Spark 叢集進行設定，並已上傳 Jupyter Notebook，您已準備要逐步執行對應至這三個 Notebook 的主題，其示範如何使用交叉驗證和超參數牽涉來建立、使用和模型化。

**模型建立︰**遵循[使用 Spark MLlib 工具組來建立資料的二進位分類和迴歸模型 ](machine-learning-data-science-spark-data-exploration-modeling.md)主題來建立這裡要評分和評估的機器學習模型。

**模型耗用量︰**若要瞭解如何評分及評估本主題中所建立的分類和迴歸模型，請參閱[評分及評估 Spark 建置機器學習模型](machine-learning-data-science-spark-model-consumption.md)。

<!---HONumber=AcomDC_0420_2016-->