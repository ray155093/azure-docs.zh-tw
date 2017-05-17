---
title: "自動調整 Azure Batch 集區中的計算節點 | Microsoft Docs"
description: "在雲端集區上啟用自動調整，以動態調整集區中的計算節點數目。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 85a2fac77cf5beae6debad044df169301d43f4ca
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>建立自動調整公式來調整 Batch 集區中的計算節點

透過自動調整功能，Azure Batch 服務可以根據您定義的參數，動態新增或移除集區中的計算節點。 自動調整應用程式所使用的計算節點數目，可讓您節省時間與金錢。 自動調整可讓您隨著作業的工作需求提高來新增節點，並且在需求降低時將它們移除。

您可讓您定義的「自動調整公式」與計算節點的集區產生關聯，以在該集區上啟用自動調整。 例如，在 Batch .NET 中，您可以使用 [PoolOperations.EnableAutoScale][net_enableautoscale] 方法。 Batch 服務會使用自動調整公式來判斷要執行您的工作負載所需的計算節點數目。 Batch 會回應定期收集的服務計量資料。 使用此計量資料，Batch 會根據您的公式以可設定的間隔調整集區中的計算節點數目。

您可以在建立集區時或在現有的集區上啟用自動調整。 您也可以變更已啟用「自動調整」的集區上的現有的公式。 Batch 可讓您在將公式指派給集區之前評估公式，以及監視自動調整回合的狀態。

本文會討論構成自動調整公式的各種實體，包括變數、運算子、作業和函式。 您將了解如何取得 Batch 內的各種計算資源和工作度量。 您可以使用這些度量，根據資源使用量和工作狀態明智地調整集區的節點計數。 然後，您將透過使用 Batch REST 和 .NET API，了解如何建立公式以及對集區啟用自動調整。 我們將完成幾個範例公式。

> [!IMPORTANT]
> 每個 Azure Batch 帳戶限制為可用於處理的核心 (以及計算節點) 數目上限。 Batch 服務建立的新節點數目最多達到該核心限制。 Batch 服務不會達到自動調整公式所指定的目標計算節點數目。 如需檢視和增加帳戶配額的相關資訊，請參閱 [Azure Batch 服務的配額和限制](batch-quota-limit.md) 。
>
>

## <a name="automatic-scaling-formulas"></a>自動調整公式
自動調整公式是您定義的字串值，其中包含一或多個陳述式。 自動調整公式已指派給集區的 [autoScaleFormula][rest_autoscaleformula] 元素 (Batch REST) 或 [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] 屬性 (Batch .NET)。 Batch 集區會使用您的公式來決定集區中可供下一個間隔處理的目標計算節點數目。 公式字串的大小不得超過 8 KB、最多只能包含 100 個陳述式 (以分號隔開)，而且可以包含換行和註解。

您可以將自動調整公式視為使用 Batch 自動調整「語言」。 公式陳述式是自由格式的運算式，可以包括服務定義的變數 (Batch 服務所定義的變數) 和使用者定義的變數 (您所定義的變數)。 它們可以使用內建類型、運算子和函式對這些值執行各種作業。 例如，陳述式可能會採用下列格式：

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

公式通常包含多個陳述式，其可對在先前陳述式中取得的值執行作業。 例如，首先我們會取得 `variable1` 的值，然後將它傳遞至函式以填入 `variable2`：

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

在自動調整公式中包含這些陳述式，以便達到集區應調整成的計算節點數目，也就是**專用節點**的**目標**數目。 此數目可能會更高、更低，或與集區中目前的節點數目相同。 Batch 服務會在特定間隔評估集區的自動調整公式 (下面會討論[自動調整間隔](#automatic-scaling-interval) )。 Batch 會將集區中的目標節點數目調整為自動調整公式在評估時指定的數目。

### <a name="sample-autoscale-formula"></a>自動調整公式範例

以下是可加以調整以適用於大部分情況的自動調整公式範例。 您可以依照需求調整範例公式中的 `startingNumberOfVMs` 和 `maxNumberofVMs` 變數。

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs, pendingTaskSamples);
```

使用此自動調整公式，一開始會建立包含單一 VM 的集區。 $PendingTasks 計量會定義執行中或已排入佇列的工作數目。 公式會尋找過去 180 秒內的平均擱置中工作數目，並據以設定 TargetDedicated。 此公式會確保 TargetDedicated 決不會超過 25 部 VM。 集區會隨著新工作的提交而自動成長。 隨著工作完成，VM 會逐一變成可用，且自動調整公式會縮小集區。

## <a name="variables"></a>變數
您可以在自動調整公式中同時使用**服務定義**和**使用者定義**的變數。 服務定義的變數會內建至 Batch 服務 -- 有些是讀寫狀態，而有些則是唯讀狀態。 使用者定義的變數是「您」  定義的變數。 在上一節中所示的範例公式中，`$TargetDedicated` 和 `$PendingTasks` 是服務定義的變數。 `startingNumberOfVMs` 和 `maxNumberofVMs` 變數是使用者定義的變數。

> [!NOTE]
> 服務定義的變數前面一律會加上貨幣符號 ($)。 針對使用者定義的變數而言，貨幣符號是選擇性的。
>
>

下表顯示 Batch 服務所定義的讀寫和唯讀變數。

您可以**取得**並**設定**這些這些服務定義的變數值，以管理集區中的計算節點：

| 讀寫服務定義變數 | 說明 |
| --- | --- |
| $TargetDedicated |集區之**專用計算節點**的**目標**數目就是集區應調整成的計算節點數目。 它是「目標」數目，因為集區可能不會達到此目標節點數目。 例如，如果在集區達到初始目標之前，後續的自動調整評估再次修改目標節點數目，則集區可能未達到目標節點數目。 如果在達到目標節點數目前便達到 Batch 帳戶節點或核心配額，也可能會發生這種情形。 |
| $NodeDeallocationOption |計算節點從集區移除時所發生的動作。 可能的值包括：<ul><li>**requeue**：立即終止工作，並將這些工作放回工作佇列，為它們重新排程。<li>**terminate**：立即終止工作，並從作業佇列移除這些工作。<li>**taskcompletion**：等待目前執行中的工作完成，然後再從集區中移除該節點。<li>**retaineddata**：等待所有本機工作保留在節點上的資料先清除，再從集區移除節點。</ul> |

您可以 **取得** 這些服務定義的變數值，以根據 Batch 服務提供的度量進行調整：

| 唯讀服務定義變數 | 說明 |
| --- | --- |
| $CPUPercent |CPU 使用量的平均百分比。 |
| $WallClockSeconds |已耗用的秒數。 |
| $MemoryBytes |已使用的平均 MB 數目。 |
| $DiskBytes |已在本機磁碟上使用的平均 GB 數目。 |
| $DiskReadBytes |已讀取的位元組數目。 |
| $DiskWriteBytes |已寫入的位元組數目。 |
| $DiskReadOps |已執行的讀取磁碟作業計數。 |
| $DiskWriteOps |已執行的寫入磁碟作業計數。 |
| $NetworkInBytes |輸入位元組的數目。 |
| $NetworkOutBytes |輸出位元組的數目。 |
| $SampleNodeCount |計算節點的計數。 |
| $ActiveTasks |處於作用中狀態的工作數目。 |
| $RunningTasks |處於執行中狀態的工作數目。 |
| $PendingTasks |$ActiveTasks 和 $RunningTasks 的總和。 |
| $SucceededTasks |已成功完成的工作數目。 |
| $FailedTasks |失敗的工作數目。 |
| $CurrentDedicated |目前的專用計算節點數目。 |
| $PreemptedNodeCount | 優先佔用狀態的集區中之節點數目。 |

> [!TIP]
> 上述服務定義的唯讀變數是可提供各種方法來存取相關聯資料的「物件」。 如需詳細資訊，請參閱下面的[取得範例資料](#getsampledata)。
>
>

## <a name="types"></a>類型
公式中支援以下 **類型** 。

* double
* doubleVec
* doubleVecList
* string
* timestamp：timestamp 是包含下列成員的複合結構：

  * 年
  * month (1-12)
  * day (1-31)
  * weekday (以數字格式表示，例如 1 代表星期一)
  * hour (以 24 小時制數字格式表示，例如 13 代表下午 1:00)
  * minute (00-59)
  * second (00-59)
* timeinterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>作業
以上列出的類型允許這些 **作業** 。

| 作業 | 支援的運算子 | 結果類型 |
| --- | --- | --- |
| double 運算子  double |+、-、*、/ |double |
| double 運算子  timeinterval |* |timeinterval |
| doubleVec 運算子  double |+、-、*、/ |doubleVec |
| doubleVec 運算子  doubleVec |+、-、*、/ |doubleVec |
| timeinterval 運算子  double |*、/ |timeinterval |
| timeinterval 運算子  timeinterval |+、- |timeinterval |
| timeinterval 運算子  timestamp |+ |timestamp |
| timestamp 運算子  timeinterval |+ |timestamp |
| timestamp  timestamp |- |timeinterval |
| double |-、! |double |
| timeinterval |- |timeinterval |
| double 運算子  double |<、<=、==、>=、>、!= |double |
| string  string |<、<=、==、>=、>、!= |double |
| timestamp  timestamp |<、<=、==、>=、>、!= |double |
| timeinterval 運算子  timeinterval |<、<=、==、>=、>、!= |double |
| double 運算子  double |&&, &#124;&#124; |double |

測試具有三元運算子的雙精準數 (`double ? statement1 : statement2`) 時，非零為 **true**，而零則為 **false**。

## <a name="functions"></a>Functions
這些預先定義的 **函式** 可供您用來定義自動調整公式。

| 函式 | 傳回類型 | 說明 |
| --- | --- | --- |
| avg(doubleVecList) |double |傳回 doubleVecList 中所有值的平均值。 |
| len(doubleVecList) |double |傳回 doubleVecList 建立的向量的長度。 |
| lg(double) |double |傳回 double 的對數底數 2。 |
| lg(doubleVecList) |doubleVec |傳回 doubleVecList 的全元件對數底數 2。 vec(double) 必須針對此參數明確傳遞。 否則會假設為 double lg(double) 版本。 |
| ln(double) |double |傳回 double 的自然底數。 |
| ln(doubleVecList) |doubleVec |傳回 doubleVecList 的全元件對數底數 2。 vec(double) 必須針對此參數明確傳遞。 否則會假設為 double lg(double) 版本。 |
| log(double) |double |傳回 double 的對數底數 10。 |
| log(doubleVecList) |doubleVec |傳回 doubleVecList 的全元件對數底數 10。 vec(double) 必須針對單一 double 參數明確傳遞。 否則，會假設為 double log (double) 版本。 |
| max(doubleVecList) |double |傳回 doubleVecList 中的最大值。 |
| min(doubleVecList) |double |傳回 doubleVecList 中的最小值。 |
| norm(doubleVecList) |double |傳回 doubleVecList 建立的向量的 2-norm。 |
| percentile(doubleVec v, double p) |double |傳回向量 v 的百分位數元素。 |
| rand() |double |傳回介於 0.0 到 1.0 之間的隨機值。 |
| range(doubleVecList) |double |傳回 doubleVecList 中最小和最大值之間的差異。 |
| std(doubleVecList) |double |傳回 doubleVecList 中值的標準差範例。 |
| stop() | |停止評估自動調整運算式。 |
| sum(doubleVecList) |double |傳回 doubleVecList 所有元件的總和。 |
| time(string dateTime="") |timestamp |如果未傳遞參數，則傳回目前時間的時間戳記，如果有傳遞參數，則為 dateTime 字串的時間戳記。 支援的 dateTime 格式為 W3C-DTF 和 RFC 1123。 |
| val(doubleVec v, double i) |double |傳回向量 v 中位置 i 的元素值，起始索引為零。 |

上表中所述的某些函式可以接受清單作為引數。 逗號分隔清單是 *double* 和 *doubleVec* 的任意組合。 例如：

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

評估之前，*doubleVecList* 值會轉換成單一的 *doubleVec*。 例如，如果 `v = [1,2,3]`，呼叫 `avg(v)` 就相當於呼叫 `avg(1,2,3)`。 呼叫 `avg(v, 7)` 就相當於呼叫 `avg(1,2,3,7)`。

## <a name="getsampledata"></a>取得樣本資料
自動調整公式會對 Batch 服務所提供的度量資料 (範例) 產生作用。 公式會根據從服務取得的值擴大或縮減集區大小。 上述服務定義的變數是可提供各種方法來存取與該物件相關聯資料的物件。 例如，下列運算式顯示取得最後五分鐘的 CPU 使用量的要求：

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| 方法 | 說明 |
| --- | --- |
| GetSample() |`GetSample()` 方法會傳回資料樣本的向量。<br/><br/>一個樣本有 30 秒的度量資料。 換句話說，每隔 30 秒會取得範例。 如下所述，從收集樣本到可用於公式之間會延遲。 因此，並非一段指定時間內的所有樣本都可供公式評估。<ul><li>`doubleVec GetSample(double count)`<br/>指定要從最近收集的樣本中取得的樣本數。<br/><br/>`GetSample(1)` 會傳回最後一個可用的樣本。 不過，這不適用於 `$CPUPercent` 之類的度量，因為不可能知道「何時」收集到樣本。 此範例可能是最新的，也可能因為系統問題，是更舊的。 在此情況下，最好使用如下所示的時間間隔。<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>指定收集樣本資料的時間範圍。 它也會選擇性地指定在要求的時間範圍內必須可用的樣本數百分比。<br/><br/>如果 CPUPercent 歷程記錄中有最後十分鐘的所有樣本，則 `$CPUPercent.GetSample(TimeInterval_Minute * 10)` 會傳回 20 個樣本。 不過，如果最後一分鐘的歷程記錄無法使用，則只會傳回 18 個樣本。 在此案例中：<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` 會失敗，因為只有 90% 的樣本可用。<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` 會成功。<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>指定收集資料的時間範圍 (含開始時間和結束時間)。<br/><br/>如上所述，從收集樣本到可用於公式之間會延遲。 使用 `GetSample` 方法時必須考慮這點。 請參閱下文中的 `GetSamplePercent`。 |
| GetSamplePeriod() |傳回歷史範例資料集中取得範例的期間。 |
| Count() |傳回度量歷程記錄中的範例總數。 |
| HistoryBeginTime() |傳回度量的最舊可用資料範例的時間戳記。 |
| GetSamplePercent() |傳回指定的時間間隔內可用的樣本百分比。 例如：<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>因為 `GetSample` 方法在傳回樣本的百分比小於指定的 `samplePercent` 時會失敗，因此，您可以先使用 `GetSamplePercent` 方法進行檢查。 然後您可以在樣本不足時執行替代動作，而不暫停自動調整評估。 |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>樣本、樣本百分比和 GetSample()  方法
自動調整公式的核心是要取得工作和資源度量資料，然後根據該資料調整集區大小。 因此，請務必清楚了解自動調整公式如何與度量資料或「樣本」互動。

**範例**

Batch 服務會定期取得工作和資源度量的樣本  ，使其可供自動調整公式使用。 Batch 服務會每隔 30 秒記錄一次這些樣本。 不過，通常會有一些延誤，以致記錄樣本的時間與樣本可供自動調整公式使用 (與讀取) 的時間之間有所延遲。 此外，由於各種因素 (例如網路或其他基礎結構問題)，可能未在特定間隔內記錄樣本。 這會導致「遺漏」的範例。

**樣本百分比**

將 `samplePercent` 傳遞至 `GetSample()` 方法，或呼叫 `GetSamplePercent()` 方法時，"percent" 是指 Batch 服務「可能」記錄的樣本總數，與自動調整公式實際「可用」的樣本數目之間的比較。

讓我們以 10 分鐘的時間範圍為例。 因為會每隔 30 秒記錄一次樣本，所以在 10 分鐘的時間範圍內，Batch 服務所記錄的樣本總數就已達到 20 個樣本 (每分鐘 2 個)。 不過，由於回報機制固有的延遲，或 Azure 基礎結構中的一些其他問題，可能只有 15 個樣本可供自動調整公式讀取。 這表示，在這 10 分鐘的期間內，記錄的樣本總數只有 **75%** 實際可供您的公式使用。

**GetSample() 和樣本範圍**

您的自動調整公式即將擴大和縮減您的集區--新增節點或移除節點。 因為節點為付費使用，您想要確保您的公式使用根據充足資料的明智的分析方法。 因此，建議您在公式中使用趨勢類型分析。 此類型會根據所收集樣本的範圍  來擴大和縮減集區。

若要這樣做，請使用 `GetSample(interval look-back start, interval look-back end)` 傳回樣本的 **向量** ：

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Batch 評估上述程式碼後，它會以值的向量形式傳回樣本範圍。 例如：

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

收集樣本向量後，您便可使用 `min()`、`max()` 和 `avg()` 等函式從所收集的範圍衍生出有意義的值。

若要增加安全性，如果特定一段時間可用的樣本小於特定百分比，您可以強制公式評估為「失敗」  。 強制公式評估為失敗會指示 Batch 在指定的樣本百分比無法使用時停止進一步評估公式。 在此情況下，不會變更集區大小。 若要指定評估成功所需的樣本百分比，請將其指定為 `GetSample()`的第三個參數。 以下指定了 75% 的樣本需求：

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

因為樣本可用性可能延遲，所以請務必記得指定回顧開始時間早於一分鐘的時間範圍。 樣本需要花大約一分鐘的時間才能傳播到整個系統，所以通常無法使用 `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` 範圍中的樣本。 同樣地，您可以使用 `GetSample()` 的百分比參數來強制特定樣本百分比需求。

> [!IMPORTANT]
> 我們**強烈建議**您***避免「只」*`GetSample(1)`依賴自動調整公式中的** 。 這是因為 `GetSample(1)` 基本上會向 Batch 服務表示：「不論您多久以前擷取最後一個樣本，請將它提供給我」。 因為它只是單一樣本，而且可能是較舊的樣本，所以可能無法代表最近工作或資源狀態的全貌。 如果您使用 `GetSample(1)`，請確定它是較大的陳述式，而且不是您的公式所依賴的唯一資料點。
>
>

## <a name="metrics"></a>度量
您可以在定義公式時使用**資源**和**工作**計量。 您會根據您取得和評估的度量資料來調整集區中專用節點的目標數目。 如需每個度量的詳細資訊，請參閱上面的 [變數](#variables) 一節。

<table>
  <tr>
    <th>度量</th>
    <th>說明</th>
  </tr>
  <tr>
    <td><b>Resource</b></td>
    <td><p><b>資源計量</b>是以計算節點的 CPU、頻寬和記憶體使用量以及節點數目為基礎。</p>
        <p> 這些服務定義的變數適合用於根據節點計數進行調整：</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>這些服務定義的變數適合用於根據節點資源使用量進行調整：</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Task</b></td>
    <td><p><b>工作計量</b>是根據狀態的工作，例如使用中、暫止和已完成。 下列服務定義的變數適合用於根據工作度量進行集區大小調整：</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>撰寫自動調整公式
您建置自動調整公式的方式是使用上述元件撰寫陳述式，再將這些陳述式結合成完整的公式。 在本節中，我們將建立可以執行一些真實世界調整決策的範例自動調整公式。

首先，讓我們定義新自動調整公式的需求。 公式應該︰

1. 如果 CPU 使用率偏高，則**增加**集區中計算節點的目標數目。
2. 當 CPU 使用率偏低時，**減少**集區中計算節點的目標數目。
3. 一律以 400 為節點的數目**上限**。

為了在高 CPU 使用率期間「增加」節點數目，我們將陳述式定義為在使用者定義的變數 (`$totalNodes`) 中填入 110% 的節點目前目標數目值，但僅限在過去 10 分鐘期間平均 CPU 使用率下限大於 70%。 否則，我們會使用目前的專用值。

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

為了「減少」低 CPU 使用率期間的節點數目，如果在過去 60 分鐘內平均 CPU 使用率低於 20%，公式中的下一個陳述式會將相同的 `$totalNodes` 變數設定為 90% 的節點目前目標數目。 否則，使用我們在上述陳述式中填入的目前 `$totalNodes` 值。

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

現在將專用計算節點的目標數目設定為 **上限** 400 個：

```
$TargetDedicated = min(400, $totalNodes)
```

以下是完整的公式：

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>建立啟用自動調整的集區
若要建立已啟用自動調整的新集區，您可以使用下列其中一項技術︰

**Batch .NET**

1. 使用 [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) 建立集區。
2. 將 [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) 屬性設定為 `true`。
3. 使用自動調整公式來設定 [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) 屬性。
4. (選擇性) 設定 [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) 屬性 (預設值為 15 分鐘)。
5. 使用 [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) 或 [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx) 認可集區。

**Batch REST API**

* [將集區新增至帳戶](https://msdn.microsoft.com/library/azure/dn820174.aspx)︰指定 REST API 要求中的 `enableAutoScale` 和 `autoScaleFormula` 元素，以在建立集區時為其設定自動調整。

下列程式碼片段使用 [Batch .NET][net_api] 程式庫來建立已啟用自動調整的集區。 集區的自動調整公式會將星期一的節點目標數目設定為 5，而將一週其他各天的節點目標數目設定為 1。 [自動調整間隔](#automatic-scaling-interval) 設定為 30 分鐘。 在本文中的此部分與其他 C# 程式碼片段中，"myBatchClient" 是適當初始化的 [BatchClient][net_batchclient] 執行個體。

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

除了 Batch REST API 和 .NET SDK，您可以使用任何其他 [Batch SDK](batch-apis-tools.md#azure-accounts-for-batch-development)、[Batch PowerShell Cmdlet](batch-powershell-cmdlets-get-started.md) 和 [Batch CLI](batch-cli-get-started.md) 來搭配自動調整運作。

> [!IMPORTANT]
> 當您建立已啟用自動調整的集區時，您**不得** 指定 `targetDedicated` 參數。 此外，如果您想要對已啟用自動調整的集區手動調整大小 (例如使用 [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool])，則必須先在集區**停用**自動調整，然後調整其大小。
>
>

### <a name="automatic-scaling-interval"></a>自動調整間隔
依預設，Batch 服務會根據其自動調整公式每隔 **15 分鐘**調整集區的大小。 不過，可使用下列的集區屬性設定此間隔：

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

最小間隔為 5 分鐘，而最大間隔為 168 小時。 如果指定此範圍以外的時間間隔，則 Batch 服務會傳回「不正確的要求 (400)」錯誤。

> [!NOTE]
> 自動調整目前不適合作為低於一分鐘的變更回應，而是要在您執行工作負載時逐步調整您的集區大小。
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>在現有集區啟用自動調整
如果您已經使用 targetDedicated  參數建立具有指定計算節點數目的集區，您仍可以在現有集區啟用自動調整。 每個 Batch SDK 都會提供「啟用自動調整」作業，例如︰

* [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] (Batch .NET)
* [在自動調整中啟用集區][rest_enableautoscale] (REST API)

當您在現有集區啟用自動調整時，適用下列各項：

* 如果在您發出「啟用自動調整」要求時，集區的自動調整目前**已停用**，您「必須」在發出要求時指定有效的自動調整公式。 您可以「選擇性地」指定自動調整評估間隔。 如果您未指定間隔，則會使用預設值 15 分鐘。
* 如果集區的自動調整目前**已啟用**，您可以指定自動調整公式、評估間隔，或同時指定兩者。 您不能略過這兩個屬性。

  * 如果您指定新的自動調整評估間隔，則會停止現有的評估排程並啟動新的排程。 新排程的開始時間發出「啟用自動調整」要求時的時間。
  * 如果您省略自動調整公式或評估間隔，則 Batch 服務會繼續使用該設定目前的值。

> [!NOTE]
> 如果建立集區時指定 *targetDedicated* 參數的值，則評估自動調整公式時會忽略此值。
>
>

此 C# 程式碼片段使用 [Batch .NET][net_api] 程式庫，在現有的集區上啟用自動調整：

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>更新自動調整公式
您使用相同的「啟用自動調整」要求來「更新」現有啟用自動調整集區上的公式 (例如，在 Batch .NET 中使用 [EnableAutoScale][net_enableautoscale])。 沒有特別的「更新自動調整」作業。 例如，如果在執行下列程式碼時，已在 "myexistingpool" 上啟用自動調整，則會以 `myNewFormula` 的內容取代其自動調整公式。

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>更新自動調整間隔
更新自動調整公式時，您可使用 [EnableAutoScale][net_enableautoscale] 方法來變更已啟用自動調整之現有集區的自動調整評估間隔。 例如，若要針對已啟用自動調整的集區，將自動調整評估間隔設定為 60 分鐘︰

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>評估自動調整公式
您可以先評估公式，再將它套用至集區。 如此一來，您可以執行公式的「測試執行」，以在將公式放入生產環境前，查看其陳述式的評估情況。

若要評估自動調整公式，您必須先使用**有效的公式**在集區**啟用自動調整**。 如果您想要在尚未啟用自動調整的集區上測試公式，您可以在第一次啟用自動調整時使用一行公式 `$TargetDedicated = 0`。 然後，使用下列其中之一來評估您想要測試的公式︰

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) 或 [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    這些 Batch .NET 方法都需要現有集區的識別碼以及包含要評估之自動調整公式的字串。 評估結果會包含在傳回的 [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) 執行個體中。
* [評估自動調整公式](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    在這個 REST 要求中，於 URI 中指定集區識別碼，以及於要求主體的 *autoScaleFormula* 元素中指定自動調整公式。 作業的回應會包含可能與公式相關的任何錯誤資訊。

在這個 [Batch .NET][net_api] 程式碼片段中，我們會先評估公式，再將它套用至 [CloudPool][net_cloudpool]。 如果集區並未啟用自動調整，我們會先加以啟用。

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

成功評估此程式碼片段中的公式會產生類似下列的輸出：

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>取得自動調整執行的相關資訊
若要確保您的公式如預期般執行，我們建議您定期檢查 Batch 對您的集區執行之自動調整「執行」的結果。 若要這麼做，請取得 (或重新整理) 集區的參考，並檢查其上次自動調整執行的內容。

在 Batch .NET 中，[CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) 屬性有數個屬性，可提供 Batch 服務最近在集區上執行之自動調整的相關資訊。

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

在 REST API 中，[取得集區的相關資訊](https://msdn.microsoft.com/library/dn820165.aspx)要求會傳回集區的相關資訊，其中包含 [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun) 中最近執行的自動調整資訊。

下列 C# 程式碼片段會使用 Batch .NET 程式庫來列印集區 "myPool" 上次自動調整執行的相關資訊︰

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

上述程式碼片段的範例輸出︰

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>自動調整公式範例
讓我們看看下面幾個公式，其顯示調整集區中計算資源數量的不同方式。

### <a name="example-1-time-based-adjustment"></a>範例 1：以時間為基礎的調整
或許您想要根據星期幾和時段來調整集區大小，以相應地增加或減少集區中的節點數目。

此公式會先取得目前的時間。 如果是工作日 (1-5) 且在上班時間內 (上午 8:00 - 下午 6:00)，則將目標集區大小設為 20 個節點。 否則，它會設定為 10 個節點。

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>範例 2：以工作為基礎的調整
在此範例中，是根據佇列中的工作數目調整集區大小。 請注意，公式字串中接受註解和換行。

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>範例 3：考量平行工作
這是根據工作數目調整集區大小的另一個例子。 此公式也會考慮集區已設定的 [MaxTasksPerComputeNode][net_maxtasks] 值。 已在集區上啟用 [平行工作執行](batch-parallel-node-tasks.md) 的情況下，這特別有用。

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>範例 4：設定初始集區大小
此範例顯示的 C# 程式碼片段具有自動調整公式，其在初始期間將集區大小設為一定的節點數目。 然後在初始期間經過之後，再根據執行中和作用中的工作數目來調整集區大小。

下列程式碼片段中的公式：

* 將初始的集區大小設為 4 個節點。
* 在集區生命週期的最初 10 分鐘內不調整集區大小。
* 10 分鐘後，取得過去 60 分鐘內執行中和作用中工作數目的最大值。
  * 如果這兩個值都是 0 (表示過去 60 分鐘沒有執行或作用中的工作)，集區大小就設為 0。
  * 如果其中一個值大於零，則不進行任何變更。

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>後續步驟
* [使用並行節點工作最大化 Azure Batch 計算資源使用量](batch-parallel-node-tasks.md) 包含有關如何對集區中的計算節點同時執行多項工作的詳細資料。 除了自動調整，這項功能有助於減少某些工作負載的作業持續時間，進而節省金錢。
* 另一種效率提升方式，則是確定您的 Batch 應用程式以最佳方式查詢 Batch 服務。 在 [有效率地查詢 Azure 批次服務](batch-efficient-list-queries.md)中，您將了解如何在查詢可能數千個計算節點或工作的狀態時，限制越過網路的資料量。

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

