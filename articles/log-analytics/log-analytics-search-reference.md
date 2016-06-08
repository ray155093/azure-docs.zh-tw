<properties
	pageTitle="Log Analytics 搜尋參考 | Microsoft Azure"
	description="Log Analytics 搜尋參考描述搜尋語言，並且提供一般查詢語法選項，您可以在搜尋資料及篩選運算式時用來幫助您縮小搜尋範圍。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# Log Analytics 搜尋參考

下列關於搜尋語言的參考章節描述一般查詢語法選項，您可以在搜尋資料及篩選運算式時用來幫助您縮小搜尋範圍。它也會描述您可以用來在已擷取的資料上採取動作的命令。

您可以在[搜尋欄位和 Facet 參考](#search-field-and-facet-reference)小節中閱讀搜尋中傳回的欄位，以及協助您深入鑽研相似資料類別的 Facet。

## 一般查詢語法

語法：

```
filterExpression | command1 | command2 …
```

篩選運算式 (`filterExpression`) 會定義查詢的 "where" 條件。命令會套用至查詢所傳回的結果。多個命令必須以縱線字元 ( | ) 分隔。

### 一般語法範例

範例：

```
system
```

此查詢會傳回在已編製索引的任何欄位中進行全文檢索或詞彙搜尋時包含 "system" 這個單字的結果。

>[AZURE.NOTE] 並非所有的欄位都以此方式編製索引，不過最常見的文字欄位 (例如描述和名稱) 通常都是。

```
system error
```

此查詢會傳回包含 *system* 和 *error* 單字的結果。

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

此查詢會傳回包含 *system* 和 *error* 單字的結果。然後會依據 *ManagementGroupName* 欄位 (依遞增順序)，再依據 *TimeGenerated* (依遞減順序) 排序結果。它只會採用前 10 個結果。

>[AZURE.IMPORTANT] 所有的欄位名稱以及字串和文字欄位的值都會區分大小寫。

## 篩選運算式

下列小節說明篩選運算式。

### 字串常值

字串常值是未經由剖析器辨識為關鍵字或預先定義資料類型 (例如，數字或日期) 的任何字串。

範例：

```
These all are string literals
```

此查詢會搜尋包含五個字全都出現的結果。若要執行複雜的字串搜尋，請將字串常值以引號括起來，例如：

```
" Windows Server"
```

這只會傳回結果為 "Windows Server" 的完全相符項目。

### 數字

剖析器支援數值欄位的十進位整數和浮點數語法。

範例：

```
Type:Perf 0.5
```

```
HTTP 500
```

### 日期/時間

系統中的每個資料片段都有 *TimeGenerated* 屬性，代表記錄的原始日期和時間。某些資料類型可能另外有更多的日期/時間欄位 (例如，*LastModified*)。

Log Analytics 中的時間表/時間選取器會以 *TimeGenerated* 欄位為基礎，顯示經過一段時間的結果分佈 (根據目前執行的查詢)。日期/時間欄位具有特定字串格式，可以在查詢中用來將查詢限制在特定時間範圍內。您也可以使用語法來參考相對的時間間隔 (例如，「3 天前和 2 小時前之間」)。

語法：

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


範例：

```
TimeGenerated:2013-10-01T12:20
```

前一個命令只會傳回 *TimeGenerated* 值剛好等於 2013 年 10 月 1 日 12:20 的記錄。它不太可能會提供任何結果，但您可以了解這個概念。

剖析器也支援助憶鍵的日期/時間值，NOW。


同樣地，這也不太可能產生任何結果，因為資料無法這麼快速地通過系統。

這些範例是用於相對與絕對日期的建置組塊。在接下來的三個子節中，我們將透過使用相對日期範圍的範例，說明如何在更進階的篩選器中使用它們。

### 日期/時間數學

使用日期/時間數學運算子，透過簡單的計算位移或四捨五入日期/時間值。

語法：

```
datetime/unit
```

```
datetime[+|-]count unit
```

|運算子|說明|
|---|---|
|/|將日期/時間四捨五入到指定的單位。範例：NOW/DAY 會將目前的日期/時間四捨五入至目前日期的午夜。|
|+ 或 -|將日期/時間位移指定的單位數。範例：NOW+1HOUR 會將目前的日期/時間位移早一個小時。2013-10-01T12:00-10DAYS 會將日期值往回位移 10 天。|



您可以將日期/時間數學運算子鍊結在一起，例如：

```
NOW+1HOUR-10MONTHS/MINUTE
```

下表列出受支援的日期/時間單位。

日期/時間單位|說明
---|---
YEAR, YEARS|四捨五入為目前的年份，或位移指定的年數。
MONTH, MONTHS|四捨五入為目前的月份，或位移指定的月數。
DAY, DAYS, DATE|四捨五入為目前的月份日期，或位移指定的天數。
HOUR, HOURS|四捨五入為目前的小時，或位移指定的小時數。
MINUTE, MINUTES|四捨五入為目前的分鐘，或位移指定的分鐘數。
SECOND, SECONDS|四捨五入為目前的秒，或位移指定的秒數。
MILLISECOND, MILLISECONDS, MILLI, MILLIS|四捨五入為目前的毫秒，或位移指定的毫秒數。


### 欄位 facet

藉由使用欄位 facet，您可以指定特定欄位的搜尋條件及其實際值，而非對整個索引的各種詞彙撰寫「任意文字」查詢。我們已在前幾個段落的數個範例中使用過此語法。在這裡，我們提供更複雜的範例。

**語法**

```
field:value
```

```
field=value
```

**說明**

搜尋特定值的欄位。此值可以是字串常值、數字或日期/時間。

範例：

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**語法**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**說明**

提供比較。

範例：

```
TimeGenerated>NOW+2HOURS
```

**語法**

```
field:[from..to]
```

**說明**

提供範圍面相化。

範例：

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### 邏輯運算子

查詢語言分別支援邏輯運算子 (*AND* 、 *OR* 和 *NOT*) 和它們的 C 樣式別名 (*&&* 、 *||* 和 *!*)。您可以使用括號來分組這些運算子。

範例：

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

您可以略過最上層篩選引數的邏輯運算子。在此情況下，會假設採用 AND 運算子。


篩選運算式|相當於
---|---
system error|system AND error
system "Windows Server" OR Severity:1|system AND ("Windows Server" OR Severity:1)

### 萬用字元

查詢語言支援使用 (**) 字元來代表查詢中值的一個或多個字元。

範例：

 尋找名稱中有 "SQL" 的所有電腦 (例如 "Redmond SQL")。

```
Type=Event Computer=*SQL*
```

>[AZURE.NOTE] 目前無法在引號內使用萬用字元。訊息=`"*This text*"` 將考慮使用 (\*) 做為常值 (\*) 字元。

## 命令

命令會套用至查詢所傳回的結果。使用縱線字元 ( | ) 將命令套用至擷取的結果。多個命令必須以縱線字元分隔。

>[AZURE.NOTE] 命令名稱可以大寫或小寫撰寫，與欄位名稱和資料不同。

### 排序

語法：

	sort field1 asc|desc, field2 asc|desc, …

依特定欄位排序結果。asc/desc 前置詞是選擇性的。如果將其省略，則會假設採用 *asc* 排序順序。如果查詢未明確使用 *Sort* 命令，則 Sort **TimeGenerated** desc 為預設行為，且一律會先傳回最新的結果。

### Top/Limit

語法：

	top number


	limit number
將回應限制為前 N 個結果。

範例：

	Type:Alert errors detected | top 10

傳回前 10 個比對結果。

### Skip

語法：

	skip number

略過列出結果的數目。

範例：

	Type:Alert errors detected | top 10 | skip 200

從結果 200 開始傳回 10 個比對結果。

### 選取

語法：

	select field1, field2, ...

將結果限制為您選擇的欄位。

範例：

	Type:Alert errors detected | select Name, Severity

將傳回的結果欄位限制為 *Name* 和 *Severity* 。

### Measure

*measure* 命令可用來將統計函數套用至未經處理的搜尋結果。需要取得資料的 *群組依據* 檢視時，這非常有用。當您使用 *measure* 命令時，Log Analytics 搜尋會顯示含有彙總結果的資料表。

語法：

	 measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]

	 measure aggregateFunction1([aggregatedField]) [as fieldAlias1] , aggregateFunction2([aggregatedField]) [as fieldAlias2] by groupField [interval interval]

	 measure aggregateFunction([aggregatedField])  interval interval

	 measure aggregateFunction1([aggregatedField]), aggregateFunction2([aggregatedField]), ...  interval interval


根據 *groupField* 彙總結果，並使用 *aggregatedField* 計算彙總的測量值。


|量值統計函數|說明|
|---|---|
|*aggregateFunction*|彙總函數的名稱 (不區分大小寫)。支援下列彙總函數：COUNT MAX MIN SUM AVG STDDEV COUNTDISTINCT PERCENTILE## 或 PCT## (## 是 1 到 99 之間的任意數字)|
|*aggregatedField*|正在彙總的欄位。這是 COUNT 彙總函數的選擇性欄位，但必須是 SUM、MAX、MIN、AVG STDDEV 或 PERCENTILE## 或 PCT## 的現有數值欄位 (## 是 1 到 99 之間的任意數字)。|
|*fieldAlias*|計算彙總值 (選擇性) 別名。如果未指定，欄位名稱將是 AggregatedValue。|
|*groupField*|用來分組結果集的欄位名稱。|
|*間隔*|採用下列格式的時間間隔︰**nnnNAME**，其中：nnn 是正整數。**NAME** 是間隔名稱。支援的間隔名稱包含 (區分大小寫)：MILLISECOND[S] SECOND[S] MINUTE[S] HOUR[S] DAY[S] MONTH[S] YEAR[S]|


間隔選項只能用於日期/時間群組欄位中 (例如 *TimeGenerated* 和 *TimeCreated*)。目前，服務不會強制執行，但是沒有傳遞至後端之日期/時間的欄位會造成執行階段錯誤。實作結構描述驗證時，服務 API 會拒絕某些查詢，其使用的欄位沒有間隔彙總的日期/時間。目前的 *Measure* 實作支援任何彙總函數的間隔分組。

如果省略 BY 子句，但指定間隔 (做為第二個語法)，則預設採用 *TimeGenerated* 欄位。

範例：

**範例 1**

	Type:Alert | measure count() as Count by ObjectId

*說明*

依據 *ObjectID* 將警示分組，並計算每個群組的警示數目。傳回的彙總值為 *Count* 欄位 (別名)。

**範例 2**

	Type:Alert | measure count() interval 1HOUR

*說明*

使用 *TimeGenerated* 欄位，依據 1 小時間隔將警示分組，並傳回每個間隔中的警示數目。

**範例 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*說明*

如同上述範例，但使用彙總欄位別名 (*AlertsPerHour*)。

**範例 4**

	* | measure count() by TimeCreated interval 5DAYS

*說明*

使用 *TimeCreated* 欄位，依據 5 天間隔將結果分組，並傳回每個間隔中的結果數目。

**範例 5**

	Type:Alert | measure max(Severity) by WorkflowName

*說明*

跟據工作負載名稱分組警示，並傳回每個工作流程的最大警示嚴重性值。

**範例 6**

	Type:Alert | measure min(Severity) by WorkflowName

*說明*

如同上述範例，但使用 *Min* 彙總函數。

**範例 7**

	Type:Perf | measure avg(CounterValue) by Computer

*說明*

根據 Computer 分組 Perf，並計算平均 (avg)。

**範例 8**

	Type:Perf | measure sum(CounterValue) by Computer

*說明*

如同上述範例，但使用 *Sum* 。

**範例 9**

	Type:Perf | measure stddev(CounterValue) by Computer

*說明*

如同上述範例，但使用 *STDDEV* 。

**範例 10**

	Type:Perf | measure percentile70(CounterValue) by Computer

*說明*

如同上述範例，但使用 *PERCENTILE70* 。

**範例 11**

	Type:Perf | measure pct70(CounterValue) by Computer

*說明*

如同上述範例，但使用 *PCT70* 。請注意， *PCT##* 只是 *PERCENTILE##* 函數的別名。

**範例 12**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*說明*

取得最大數目警示的前五個工作流程。

**範例 13**

	* | measure countdistinct(Computer) by Type

*說明*

計算針對每種類型報告的唯一電腦數目。

**範例 14**

	* | measure countdistinct(Computer) Interval 1HOUR

*說明*

計算針對每個小時報告的唯一電腦數目。

**範例 15**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

*說明*

根據 Computer 分組 % Processor Time，並傳回每 1 小時的平均。

**範例 16**

	Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

*說明*

根據 method 分組 W3CIISLog，並傳回代表每 5 分鐘的最大值。

**範例 17**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

*說明*

根據 computer 分組 % Processor Time，並傳回每 1 小時的最小值、平均、75 百分位數和最大值。

### Where

語法：

```
**where** AggregatedValue>20
```

只能用於 *Measure* 命令之後，以進一步篩選 *Measure* 彙總函數所產生的彙總結果。

範例：

	Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

	Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)

### IN

語法：

```
(Outer Query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

說明：此語法可讓您建立彙總，並將值清單從該彙總提供到另一個外部 (主要) 搜尋，這個搜尋將尋找具有那些值的事件。若要這麼做，您必須以大括號括住內部搜尋，並使用 IN 運算子將其結果提供做為外部搜尋中欄位的可能值。

內部查詢範例︰ *目前遺漏安全性更新的電腦* ，並使用下列彙總查詢：

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

尋找*目前遺漏安全性更新之電腦的所有 Windows 事件* 的最後查詢類以：

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### Dedup

**語法**

	Dedup FieldName

**說明** 針對所指定欄位的每個唯一值，傳回第一份找到的文件。

**範例**

	Type=Event | sort TimeGenerated DESC | Dedup EventID

上述範例會一個 EventID 傳回一個事件 (因為我們在 TimeGenerated 上使用 DESC，所以是最新事件)


### Extend

**說明** 可讓您在查詢中建立執行階段欄位

**範例 1**

	Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
顯示 SQL 評估建議的加權建議分數

**範例 2**

	Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
顯示計數器值，單位為 KB，而非位元組

**範例 3**

	Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
調整 WireData TotalBytes 的值，讓所有結果都落在 0 與 100 之間。

**範例 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
將小於 50% 的效能計數器值標記為 LOW，而將其他值標記為 HIGH
```

**支援的函數**


| 函式 |說明 |語法範例|  
|---------|---------|---------|
| abs | 傳回所指定數值或函數的絕對值。| `abs(x)` <br> `abs(-5)` |
| 和 | 只有在其所有運算元都評估為 true 時，才會傳回 true 值。 | `and(not(exists(**popularity**)),exists(**price**))` |
| def | def 是 default 的縮寫。傳回欄位 "field" 的值，或者，如果欄位不存在，則會傳回所指定的預設值，並產生第一個值，其中： `exists()==true`。 | `div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| div | `div(x,y)` 會將 x 除以 y。 | `div(1,y),div(sum(x,100),max(y,1))` |
| dist | 傳回 n 維度空間中兩個向量之間的距離 (點)。在次方中，使用 ValueSource 執行個體加上二以上的值，並計算兩個向量之間的距離。每個 ValueSource 都必須是數字。必須傳入偶數的 ValueSource 執行個體，而且此方法假設前半部分代表第一個向量，而後半部分代表第二個向量。 | `dist(2, x, y, 0, 0)` - 針對每個文件，計算 (0,0) 與 (x,y) 之間的歐幾里得距離</p><p>dist(1, x, y, 0, 0)：針對每個文件，計算 (0,0) 與 (x,y) 之間的 Manhattan (taxicab) 距離 <br> `dist(2,,x,y,z,0,0,0)` - 每個文件在 (0,0,0) 與 (x,y,z) 之間的歐幾里得距離。<br>`dist(1,x,y,z,e,f,g)` - 每個文件在 (x,y,z) 與 (e,f,g) 之間的 Manhattan (taxicab) 距離，其中每個字母都是欄位名稱。 |
| exists | 如果有欄位的任何成員，請傳回 TRUE。 | `exists(author)` - 會傳回 TRUE (如果任何文件在 "author" 欄位中有值)。<br>`exists(query(price:5.00))` - 會傳回 TRUE (如果 "price" 符合 "5.00"。 |
| hsin | Haversine 距離會計算沿著球體流動時，球體上兩點之間的距離。值必須是以弧度為單位。hsinalso 採用 Boolean 引數來指定函數是否應該將其輸出轉換為弧度。 | `hsin(2, true, x, y, 0, 0)` |
| if | 啟用條件式函數查詢。在 `if(test,value1,value2)` 中：test 是或參照可傳回邏輯值 (TRUE 或 FALSE) 的邏輯值或運算式。 `value1` 是函數在 test 產生 TRUE 時所傳回的值。 `value2` 是函數在 test 產生 FALSE 時所傳回的值。運算式可以是輸出布林值的任何函數，或甚至傳回數值的函數，在此情況下，值 0 將會解譯為 false 或字串，在此情況下，空字串會解譯為 false。 | `if(termfreq(cat,'electronics'),popularity,42)`：這個函數會檢查每個文件，確認 cat 欄位中是否包含 "electronics" 這個字。如果是的話，則會傳回 popularity 欄位的值，否則會傳回值 42。 |
| linear | 實作 `m*x+c` ，其中 m 和 c 是常數，而 x 是任意函數。這相當於 `sum(product(m,x),c)` ，但較具效率，因為它實作為單一函數。 | `linear(x,m,c) linear(x,2,4)` 傳回 `2*x+4` |
| log | 傳回所指定函數的對數底數 10。 | `log(x)   log(sum(x,100))` |
| map | 將輸入函數 x 的任何落在 min 與 max (含) 之間的值對應到指定的目標。min 和 max 引數必須是常數。target 和 default 引數可以是常數或函數。如果值 x 未落在 min 與 max 之間，則會傳回值 x，或者，如果指定為第 5 個引數，則會傳回預設值。 |  `map(x,min,max,target) map(x,0,0,1)` - 將任何值 0 變更為 1。這適用於處理預設 0 值。<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)` - 將 0 與 100 之間的任何值變更為 1，並將所有其他值都變更為 -1。<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))` - 將 0 與 100 之間的任何值變更為 x+599，並將所有其他值都變更為 text 欄位中 'solr' 這個字的次數。 |
| max | 傳回多個巢狀函數或常數的最大數值 指定為引數：`max(x,y,...)`。max 函數也適用於將某個指定常數上的另一個函數或欄位「降到最小值」 使用 `field(myfield,max)` 語法來選取單一多值欄位的最大值。 | `max(myfield,myotherfield,0)` |
| min | 傳回常數之多個巢狀函數的最小數值 指定為引數︰`min(x,y,...)`。min 函數也適用於使用常數提供函數的「上限」 使用 `field(myfield,min)` 語法來選取單一多值欄位的最小值。 | `min(myfield,myotherfield,0)` |
| ms | 傳回其引數之間差異的毫秒。日期相對於 Unix 或 POSIX 時間新紀元：1970 年 1 月 1 日午夜 UTC。引數可以是索引 TrieDateField 的名稱，或根據常數日期或 NOW 的日期算術。`ms()`︰相當於 `ms(NOW)`，新紀元後的毫秒數。`ms(a)`：傳回引數所代表之新紀元後的毫秒數。`ms(a,b)`：傳回 b 出現在 a 前面的毫秒數 (即 `a - b`) | `ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| 否 | 所包裝函數的邏輯負值。 | `not(exists(author))`：只有在 `exists(author)` 為 false 時才為 TRUE。 |
| 或 | 邏輯分離。 | `or(value1,value2)` - ：在 value1 或 value2 為 true 時為 TRUE。 |
| pow | 將指定的基底提升為指定的次方。`pow(x,y)` 將 x 提升為次方 y。 | `pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)` - 與 sqrt 相同 |
| product | 傳回多個數值或函數 (指定為逗號分隔的清單) 的乘積。`mul(...)` 也可以做為這個函數的別名。 | `product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip | 執行 `recip(x,m,a,b)` 實作 `a/(m*x+b)` 的倒數函數，其中 m、a、b 是常數，而 x 是任何任意複雜函數。a 與 b 相等而且 x>=0 時，此函式的最大值為 1，會在 x 增加時減少。同時增加 a 和 b 的值，可將整個函數移到曲線最平緩的部分。x 為 `rord(datefield)` 時，這些屬性可以將這個設為提高較新文件的理想函數。 | `recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| 級別 | 調整函數 x 的值，讓它們落在指定的 minTarget 與 maxTarget (含) 之間。目前的實作會周遊所有函數值以取得最小值和最大值，讓它可以取得正確的小數位數。目前的實作無法區別何時刪除文件或文件是否沒有值。在這些情況下，它會使用 0.0 值。這表示，如果值通常都大於 0.0，其中一個還是可以將 0.0 當成要對應來源的最小值。在這些情況下，適當的 `map()` 函數可以用做將 0.0 變更為實際範圍內之值的因應措施，如這裡所示：`scale(map(x,0,0,5),1,2)` | `scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)` - 調整 x 值，讓所有值都在 1 與 2 (含) 之間。 |
| sqedist | 平方歐幾里得距離會計算 2 基準 (歐幾里得距離)，但不會採用平方根，因此節省相當耗費資源的作業。這通常是下列情況：關心歐幾里得距離的應用程式不需要實際距離，但可以使用距離平方。必須傳入偶數的 ValueSource 執行個體，而且此方法假設前半部分代表第一個向量，而後半部分代表第二個向量。 | `sqedist(x_td, y_td, 0, 0)` |
| sqrt | 傳回所指定數值或函數的平方根。 | `sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist | 計算兩個字串之間的距離。使用 Lucene 拼字檢查程式 StringDistance 介面並支援該封裝中可用的所有實作，而且允許應用程式透過 Solr 的資源載入功能插入專屬的實作。strdist 採用 `(string1, string2, distance measure)`。距離量值的可能為：jw：Jaro-Winkler edit：Levenstein 或編輯距離 ngram：NGramDistance (指定時) 也可以選擇性地傳入 ngram 大小。預設值為 2。FQN：StringDistance 介面實作的完整類別名稱。必須要有無引數建構函式。 | `strdist("SOLR",id,edit)` |
| sub | 從 `sub(x,y)` 傳回 x-y。 | `sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| sum | 傳回多個數值或函數 (指定為逗號分隔的清單) 的總和。`add(...)` 可以做為這個函數的別名。 | `sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| xor | 其他，而非兩者。 | `xor(field1,field2)` - 如果 field1 或 field2 為 true，則傳回 TRUE；如果兩者都為 true，則傳回 FALSE。 |



## 搜尋欄位和 Facet 參考 (英文)

當您使用記錄檔搜尋來尋找資料時，結果會顯示各種欄位和 Facet。不過，您會看到資訊的某些部分可能不太清楚。您可以使用下列資訊來協助了解結果。



|欄位|搜尋類型|說明|
|---|---|---|
|TenantId|全部|用來分割資料|
|TimeGenerated|全部|用來驅動時間軸，timeselectors (在搜尋和其他螢幕中)。它代表資料片段的產生 (通常在代理程式上) 時間。時間以 ISO 格式表示，且一律為 UTC。如果「類型」是以現有的檢測 (也就是記錄檔中的事件) 為基礎，這通常就是記錄項目/列/記錄所記錄的即時時間。對於某些其他透過管理組件或在雲端中產生的類型 (也就是建議/警示/更新代理程式等等)，這是收集新的資料片段和某種組態之快照集的時間，或者根據它產生建議/警示的時間。|
|EventID|事件|Windows 事件記錄檔中的 EventID|
|EventLog|事件|Windows 在其中記錄事件的事件記錄檔|
|EventLevelName|事件|重大 / 警告 / 資訊 / 成功|
|EventLevel|事件|重大 / 警告 / 資訊 / 成功 的數值 (對於更容易/更適合閱讀的查詢，請改用 EventLevelName)|
|SourceSystem|全部|資料來自何處 (根據服務的「附加」模式 - 也就是 Operations Manager、OMS (= 雲端中產生的資料)、Azure 儲存體 (來自 WAD 的資料) 等等。|
|ObjectName|PerfHourly|Windows 效能物件名稱|
|InstanceName|PerfHourly|Windows 效能計數器執行個體名稱|
|CounteName|PerfHourly|Windows 效能計數器名稱|
|ObjectDisplayName|PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty|顯示在 Operations Manager 中效能集合規則以其為目標的物件名稱，或由 Operational Insights 探索的物件名稱，或針對其產生警示的物件名稱|
|RootObjectName|PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty|顯示在 Operations Manager 中效能集合規則以其為目標之物件父系的父系名稱 (在雙主控關聯性中：也就是由 Windows 電腦所主控之 SqlInstance 所主控的 SqlDatabase)，或由 Operational Insights 探索的物件名稱，或針對其產生警示的物件名稱|
|電腦|大部分的類型|資料所屬的電腦名稱|
|DeviceName|ProtectionStatus|資料所屬的電腦名稱 (如同 'Computer')|
|DetectionId|ProtectionStatus| |
|ThreatStatusRank|ProtectionStatus|威脅狀態排名是威脅狀態的數值表示法，類似於 HTTP 回應碼，我們在數字之間留了間距 (這就是為什麼沒有威脅是 150，而不是 100 或 0)，讓我們有一些空間可以加入新的狀態。當我們執行威脅狀態和防護狀態的彙總套件時，我們想要顯示電腦在選取時間間隔中所經歷的最差狀態。我們可以使用數字來排名不同的狀態，讓我們查看最高數字的記錄。|
|ThreatStatus|ProtectionStatus|ThreatStatus 的描述，與 ThreatStatusRank 1:1 對應。|
|TypeofProtection|ProtectionStatus|在電腦中偵測到的反惡意程式碼產品：無、Microsoft 惡意程式碼移除工具、Forefront 等等|
|ScanDate|ProtectionStatus| |
|SourceHealthServiceId|ProtectionStatus、RequiredUpdate|這台電腦之代理程式的 HealthService 識別碼|
|HealthServiceId|大部分的類型|這台電腦之代理程式的 HealthService 識別碼|
|ManagementGroupName|大部分的類型|Operations Manager 附加代理程式的管理群組名稱；否則它將會是 null/空白|
|ObjectType|ConfigurationObject|由 Log Analytics 組態評估所探索到之這個物件的類型 (例如 Operations Manager 管理組件的「類型」/類別)|
|UpdateTitle|RequiredUpdate|找到未安裝的更新名稱|
|PublishDate|RequiredUpdate|更新何時在 Microsoft Update 上發佈？|
|伺服器|RequiredUpdate|資料所屬的電腦名稱 (如同 'Computer')|
|產品|RequiredUpdate|更新適用的產品|
|UpdateClassification|RequiredUpdate|更新類型 (更新彙總套件、Service Pack 等等)|
|KBID|RequiredUpdate|描述此最佳作法或更新的 KB 文章識別碼|
|WorkflowName|ConfigurationAlert|產生警示之規則或監視器的名稱|
|嚴重性|ConfigurationAlert|警示的嚴重性|
|優先順序|ConfigurationAlert|警示的優先順序|
|IsMonitorAlert|ConfigurationAlert|此警示由監視器 (true) 或規則 (false) 所產生？|
|AlertParameters|ConfigurationAlert|使用 Log Analytics 警示之參數的 XML|
|Context|ConfigurationAlert|使用 Log Analytics 警示之 'context' 的 XML|
|工作負載|ConfigurationAlert|警示所參考的技術或「工作負載」|
|AdvisorWorkload|建議|建議所參考的技術或「工作負載」|
|說明|ConfigurationAlert|警示描述 (簡短)|
|DaysSinceLastUpdate|UpdateAgent|此代理程式在幾天前 (相對於記錄的 'TimeGenerated') 從 WSUS/Microsoft Update 安裝了任何更新？|
|DaysSinceLastUpdateBucket|UpdateAgent|根據 DaysSinceLastUpdate，多久以前的「時間值區」分類是上一次從 WSUS/Microsoft Update 安裝任何更新的電腦|
|AutomaticUpdateEnabled|UpdateAgent|自動更新檢查是在此代理程式上啟用或停用的嗎？
|AutomaticUpdateValue|UpdateAgent|自動更新檢查會設為自動下載和安裝、只下載，或只檢查嗎？|
|WindowsUpdateAgentVersion|UpdateAgent|Microsoft Update 代理程式的版本號碼|
|WSUSServer|UpdateAgent|此更新代理程式的目標是哪個 WSUS 伺服器？|
|OSVersion|UpdateAgent|此更新代理程式在其中執行的作業系統版本|
|名稱|建議，ConfigurationObjectProperty|來自 Log Analytics 組態評估的建議名稱/標題或屬性名稱|
|值|ConfigurationObjectProperty|來自 Log Analytics 組態評估的屬性值|
|KBLink|建議|描述此最佳作法或更新的 KB 文章 URL|
|RecommendationStatus|建議|建議為一些類型，其記錄會「更新」，而不只是加入至搜尋索引。此狀態會變更建議是否為作用中/開啟，或者 Log Analytics 是否會偵測到狀態已解決。|
|RenderedDescription|事件|Windows 事件的呈現描述 (具有填入參數的重複使用文字)|
|ParameterXml|事件|使用 Windows 事件「資料」區段中之參數的 XML (如同在事件檢視器中所見)|
|EventData|事件|使用 Windows 事件之完整「資料」區段的 XML (如同在事件檢視器中所見)|
|來源|事件|產生事件的事件記錄檔來源|
|EventCategory|事件|事件的類別，直接來自 Windows 事件記錄檔|
|UserName|事件|Windows 事件的使用者名稱 (通常是 NT AUTHORITY\\LOCALSYSTEM)|
|SampleValue|PerfHourly|效能計數器每小時彙總的平均值|
|Min|PerfHourly|效能計數器每小時彙總的每小時間隔內最小值|
|Max|PerfHourly|效能計數器每小時彙總的每小時間隔內最大值|
|Percentile95|PerfHourly|效能計數器每小時彙總的每小時間隔內第 95 個百分位數|
|SampleCount|PerfHourly|多少「未經處理的」效能計數器範例用來產生此每小時彙總記錄|
|Threat|ProtectionStatus|找到的惡意程式碼名稱|
|StorageAccount|W3CIISLog|從其中讀取記錄的 Azure 儲存體帳戶|
|AzureDeploymentID|W3CIISLog|記錄所屬的雲端服務之 Azure 部署識別碼|
|角色|W3CIISLog|記錄所屬的 Azure 雲端服務之角色|
|RoleInstance|W3CIISLog|記錄所屬的 Azure 角色之 RoleInstance|
|sSiteName|W3CIISLog|記錄所屬的 IIS 網站 (metabase 標記法)；在原始記錄中的 s-sitename 欄位|
|sComputerName|W3CIISLog|在原始記錄中的 s-computername 欄位|
|sIP|W3CIISLog|HTTP 要求被定址的目標伺服器 IP 位址。在原始記錄中的 s-ip 欄位|
|csMethod|W3CIISLog|用戶端在 HTTP 要求中使用的 HTTP 方法 (GET/POST/等等)。在原始記錄中的 cs-method|
|cIP|W3CIISLog|HTTP 要求的來源用戶端 IP 位址。在原始記錄中的 c-ip 欄位|
|csUserAgent|W3CIISLog|由用戶端宣告的 HTTP 使用者代理程式 (瀏覽器或其他方式)。在原始記錄中的 cs-user-agent|
|scStatus|W3CIISLog|由伺服器傳回給用戶端的 HTTP 狀態碼 (200/403/500/等等)。在原始記錄中的 cs-status|
|TimeTaken|W3CIISLog|完成要求所花費的時間 (以毫秒為單位)。在原始記錄中的 timetaken 欄位|
|csUriStem|W3CIISLog|要求的相對 Uri (沒有主機位址，也就是 '/search' ) 的要求。在原始記錄中的 cs-uristem 欄位|
|csUriQuery|W3CIISLog|URI 查詢。只有 ASP 頁面等動態頁面才需要 URI 查詢，所以此欄位通常包含靜態網頁的連字號。|
|sPort|W3CIISLog|傳送 HTTP 要求的目標伺服器連接埠 (也是 IIS 接聽的目標，因為它會挑選它)|
|csUserName|W3CIISLog|已驗證的使用者名稱，如果要求已通過驗證且不匿名|
|csVersion|W3CIISLog|使用於要求中的 HTTP 通訊協定版本 (也就是'HTTP/1.1')|
|csCookie|W3CIISLog|Cookie 資訊|
|csReferer|W3CIISLog|使用者上次造訪的網站。這個網站提供目前網站的連結。|
|csHost|W3CIISLog|要求的主機標頭 (也就是 'www.mysite.com')|
|scSubStatus|W3CIISLog|子狀態錯誤碼|
|scWin32Status|W3CIISLog|Windows 狀態碼|
|csBytes|W3CIISLog|在要求中從用戶端傳送到伺服器的位元組|
|scBytes|W3CIISLog|在回應中從伺服器傳回給用戶端的位元組|
|ConfigChangeType|ConfigurationChange|變更的類型 (WindowsServices / Software / 等等)|
|ChangeCategory|ConfigurationChange|變更的類別 (已修改 / 已新增 / 已移除)|
|SoftwareType|ConfigurationChange|軟體的類型 (更新 / 應用程式)|
|SoftwareName|ConfigurationChange|軟體的名稱 (僅適用於軟體變更)|
|發佈者|ConfigurationChange|發佈軟體的供應商 (僅適用於軟體變更)|
|SvcChangeType|ConfigurationChange|已套用在 Windows 服務的變更類型 (State / StartupType / Path / ServiceAccount) - 僅適用於 Windows 服務變更|
|SvcDisplayName|ConfigurationChange|顯示已變更的服務名稱|
|SvcName|ConfigurationChange|已變更的服務名稱|
|SvcState|ConfigurationChange|要求的新 (目前) 狀態|
|SvcPreviousState|ConfigurationChange|服務的上一個已知狀態 (僅適用於服務狀態變更時)|
|SvcStartupType|ConfigurationChange|服務啟動類型|
|SvcPreviousStartupType|ConfigurationChange|上一個服務啟動類型 (僅適用於服務啟動類型變更時)|
|SvcAccount|ConfigurationChange|服務帳戶|
|SvcPreviousAccount|ConfigurationChange|先前的服務帳戶 (僅適用於服務帳戶變更時)|
|SvcPath|ConfigurationChange|Windows 服務的可執行檔路徑|
|SvcPreviousPath|ConfigurationChange|Windows 服務先前的可執行檔路徑 (僅適用於其變更時)|
|SvcDescription|ConfigurationChange|服務的描述|
|上一步|ConfigurationChange|此軟體先前的狀態 (已安裝 / 未安裝 / 上一個版本)|
|Current|ConfigurationChange|此軟體最新的狀態 (已安裝 / 未安裝 / 上一個版本)|

## 後續步驟
如需記錄檔搜尋的其他資訊：

- 熟悉[記錄檔搜尋](log-analytics-log-searches.md)以檢視方案所收集的詳細資訊。
- 使用 [Log Analytics 中的自訂欄位](log-analytics-custom-fields.md)來延伸記錄檔搜尋。

<!---HONumber=AcomDC_0504_2016-->