<properties 
	pageTitle="機器學習應用程式：異常偵測服務 |Microsoft Azure" 
	description="「異常偵測 API」是一個搭配 Microsoft Azure Machine Learning 建置的範例，此 API 使用固定時間間隔的數值，偵測時間序列資料中的異常狀況。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="alokkirpal" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="06/29/2016" 
	ms.author="alokkirpal"/>


# 機器學習異常偵測服務#

##概觀

「異常偵測 API」是一個搭配 Azure Machine Learning 建置的範例，此 API 使用固定時間間隔的數值，偵測時間序列資料中的異常狀況。

此 API 可偵測時間序列資料的下列異常模式類型︰

* **正向和負向趨勢**：例如，在監視運算中所使用的記憶體數量時，趨勢向上可能會引起您的注意，因為這可能表示有記憶體流失。

* **動態範圍值有所變更**：例如，在監視雲端服務所擲回的例外狀況時，若動態範圍值有任何變更，可能表示服務的健康狀態不穩定。

* **尖峰和下降**：例如，在監視服務的登入失敗數目或電子商務網站的結帳數目時，若有尖峰或下降可能表示發生異常行為。

這些 Machine Learning 偵測器會追蹤數值在不同時間所發生的這一類變化，並以異常分數的形式報告數值正在進行的變化。它們不需要臨機操作調整臨界值，而且其分數可用來控制偽陽性率。異常偵測 API 可用在許多案例，例如追蹤不同時間的 KPI 以監視服務、透過搜尋次數、點擊次數等度量來監視使用量、透過不同時間的記憶體、CPU、檔案讀取數等計數器來監視效能。

異常偵測在供應時會隨附有用的工具以讓您快速入門。
* [Web 應用程式](http://anomalydetection-aml.azurewebsites.net/)可協助您評估和視覺化將異常偵測 API 套用在您的資料後所產生的結果。
* [範例程式碼](http://adresultparser.codeplex.com/)示範如何使用 C#，以程式設計方式存取 API 並剖析結果。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]


##API 定義

此服務提供透過 HTTPS 的 REST 型 API，有各種不同方式可使用此 API，包括 Web 或行動應用程式、R、Python、Excel 等等。您可以透過 REST API 呼叫將您的時間序列資料傳送到此服務，它會執行上述三個異常類型的組合。服務會在 Azure Machine Learning 平台上執行，順暢調整您的商業需求並提供 SLA。

###標頭
請確定要求中包含正確的標頭，應該如下：

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

您可以在 [Azure 資料市場](https://datamarket.azure.com/account/keys)中找到您帳戶中的帳戶金鑰。

###分數 API

分數 API 可用來對非季節性的時間序列資料執行異常偵測。API 會對此資料執行許多異常偵測器，並傳回其異常分數。下圖顯示分數 API 可偵測到的異常範例。此時間序列有 2 個不同的層級變更和 3 個尖峰。紅點顯示偵測到層級變更時的時間，黑點則顯示偵測到的尖峰。

![分數 API][1]
	
**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**要求本文範例**

在下面的要求中，我們會傳送具有 3/1/2016 到 3/10/2016 之資料點的時間序列 (所顯示的內容並不完全) 和尖峰偵測器參數到 API，以偵測這些資料點是否有任何異常。

	{
	  "data": [
	    [ "9/21/2014 11:05:00 AM", "3" ],
	    [ "9/21/2014 11:10:00 AM", "9.09" ],
	    [ "9/21/2014 11:15:00 AM", "0" ]
	  ],
	  "params": {
	    "tspikedetector.sensitivity": "3",
	    "zspikedetector.sensitivity": "3",
	    "trenddetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "postprocess.tailRows": "2"
	  }
	}

此要求的回應會是︰

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
	  "ADOutput":"{
	    "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
		"ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
		"Values":[
		  ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
		  ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
		]
	  }"
	}

###ScoreWithSeasonality API

ScoreWithSeasonality API 可用來對具有季節性模式的時間序列執行異常偵測。這個 API 適用於偵測季節性模式的偏差。

下圖顯示季節性時間序列中所偵測到的異常範例。時間序列有一個尖峰 (第 1 個黑點)、兩個下降 (第 2 個黑點和尾端的黑點)，以及一個層級變更 (紅點)。請注意，從時間序列中移除季節性元件後，才能看到序列中間的兩個下降和該層級變更。

![季節性 API][2]

**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**要求本文範例**

在下面的要求中，我們會傳送具有 3/1/2016 到 3/10/2016 之資料點的時間序列 (所顯示的內容並不完全) 和參數到 API，以偵測這些資料點是否有任何異常。

	{
	  "data": [
	    [ "9/21/2014 11:10:00 AM", "9" ],
	    [ "9/21/2014 11:15:00 AM", "12" ],
	    [ "9/21/2014 11:20:00 AM", "10" ]
	  ],
	  "params": {
	    "preprocess.aggregationInterval": "0",
	    "preprocess.aggregationFunc": "mean",
	    "preprocess.replaceMissing": "lkv",
	    "postprocess.tailRows": "1",
	    "zspikedetector.sensitivity": "3",
	    "tspikedetector.sensitivity": "3",
	    "upleveldetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "trenddetector.sensitivity": "3.25",
	    "detectors.historywindow": "500",
	    "seasonality.enable": "true",
	    "seasonality.transform": "deseason",
	    "seasonality.numSeasonality": "1"
	  }
	}

此要求的回應會是︰

	{
		"odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
		"ADOutput": "{
			"ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
		  	"ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
		  	"Values":[
		    	["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
		  	]
		}"
	}

###偵測器

異常偵測 API 支援 3 大類別的偵測器。下表中可以找到每個偵測器的特定輸入參數和輸出的詳細資料。

|偵測器類別|偵測器|說明|輸入參數|輸出
|---|---|---|---|---|
|尖峰偵測器|TSpike 偵測器|根據所設定的敏感度偵測尖峰和下降|tspikedetector.sensitivity：接受範圍 1 - 10 的整數值，預設值︰3；值愈高，敏感度越低|TSpike︰二進位值 - 如果偵測到尖峰/下降則為 ‘1’，否則為 ‘0’|
|ZSpike 偵測器|根據所設定的敏感度偵測尖峰和下降|zspikedetector.sensitivity：接受範圍 1 - 10 的整數值，預設值︰3；值愈高，敏感度越低|ZSpike︰二進位值 - 如果偵測到尖峰/下降則為 ‘1’，否則為 ‘0’|
|緩慢趨勢偵測器|緩慢趨勢偵測器|根據所設定的敏感度偵測緩慢的正向趨勢|trenddetector.sensitivity：偵測器分數的臨界值 (預設值︰3.25，3.25 – 5 是合理值的選取範圍；值愈高，敏感度越低)|tscore︰代表趨勢異常分數的浮動數字|
|層級變更偵測器|單向層級變更偵測器|根據所設定的敏感度偵測向上層級變更|同緩慢趨勢偵測器|pscore︰代表向上層級變更異常分數的浮動數字|
|雙向層級變更偵測器|根據所設定的敏感度偵測向上和向下層級變更|同緩慢趨勢偵測器|rpscore︰代表向上和向下層級變更異常分數的浮動數字

###參數

下表列出這些輸入參數的詳細資訊：

|輸入參數|說明|預設設定|類型|有效範圍|建議範圍|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|用來彙總輸入時間序列的彙總間隔 (秒)|0 (不執行彙總)|integer|0︰略過彙總，否則 > 0|5 分鐘到 1 天，取決於時間序列
|preprocess.aggregationFunc|用來將資料彙總到指定 AggregationInterval 的函式|平均值|列舉|平均值、總和、長度|N/A|
|preprocess.replaceMissing|用來插補遺漏資料的值|lkv (上一個已知值)|列舉|零、lkv、平均值|N/A|
|detectors.historyWindow|用於計算異常分數的歷程記錄 (以資料點數目為單位)|500|integer|10 - 2000|取決於時間序列|
|upleveldetector.sensitivity|向上層級變更偵測器的敏感度。 |3\.25|double|None|3\.25 - 5 (值愈低代表敏感度越高)|
|bileveldetector.sensitivity|雙向層級變更偵測器的敏感度。 |3\.25|double|None|3\.25 - 5 (值愈低代表敏感度越高)|
|trenddetector.sensitivity|正向趨勢偵測器的敏感度。 |3\.25|double|None|3\.25 - 5 (值愈低代表敏感度越高)|
|tspikedetector.sensitivity |TSpike 偵測器的敏感度|3|integer|1 - 10|3 - 5 (值愈低代表敏感度越高)|
|zspikedetector.sensitivity |ZSpike 偵測器的敏感度|3|integer|1 - 10 |3 - 5 (值愈低代表敏感度越高)|
|seasonality.enable|是否要執行季節性分析|true|布林值|true、false|取決於時間序列|
|seasonality.numSeasonality |要偵測的定期循環數目上限|1|integer|1、2|1 - 2|
|seasonality.transform |在套用異常偵測之前，是否應該移除季節性 (和) 趨勢元件|deseason|列舉|無、deseason、deseasontrend|N/A|
|postprocess.tailRows |輸出結果中要保留的最新資料點數目|0|integer|0 (保留所有資料點)，或指定要在結果中保留的資料點數目|N/A|

###輸出
API 會對您的時間序列資料執行所有偵測器，然後傳回每個時間點的異常分數和二進位尖峰指示器。下表列出 API 的輸出。

|輸出|說明|
|---|---|
|時間|未經處理資料或彙總 (和/或) 插補資料 (如果套用彙總 (和/或) 遺漏資料插補) 的時間戳記|
|OriginalData|未經處理資料或彙總 (和/或) 插補資料 (如果套用彙總 (和/或) 遺漏資料插補) 的值|
|ProcessedData|下列任一項︰<ul><li>已進行季節性調整的時間序列，如果偵測到明顯季節性並選取了 deseason 選項；</li><li>已進行季節性調整並去趨勢化的時間序列，如果偵測到明顯季節性並選取了 deseasontrend 選項</li><li>否則，與 OriginalData 相同</li>|
|TSpike|指出 TSpike 偵測器是否要偵測尖峰的二進位指示器|
|ZSpike|指出 Zspike 偵測器是否要偵測尖峰的二進位指示器|
|Pscore|代表向上層級變更異常分數的浮動數字|
|Palert|根據輸入敏感度指出有向上層級變更異常的 1/0 值|
|RPScore|代表雙向層級變更異常分數的浮動數字|
|RPAlert|根據輸入敏感度指出有雙向層級變更異常的 1/0 值|
|TScore|代表正向趨勢異常分數的浮動數字|
|TAlert|根據輸入敏感度指出有正向趨勢異常的 1/0 值|


若要剖析此輸出，可使用[簡單的剖析器](https://adresultparser.codeplex.com/) - 其具有的範例程式碼可示範如何連線到 API 並剖析輸出。所偵測到的異常可以具體呈現在儀表板中並 (或) 傳送給專家人員以採取更正動作或整合到票證系統。


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 

<!---HONumber=AcomDC_0629_2016-->