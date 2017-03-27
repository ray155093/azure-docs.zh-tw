---
title: "在 HDInsight 中搭配 Hive 與 Pig 來使用 Python | Microsoft Docs"
description: "了解如何在 HDInsight 中從 Hive 和 Pig (Azure 上的 Hadoop 技術堆疊) 使用 Python 使用者定義函數 (UDF)。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: b39c913367928e8e98dfb1d6bfdca75fcded13c3
ms.lasthandoff: 03/01/2017

---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>在 HDInsight 上使用 Python 使用者定義函數 (UDF) 與 Hive 和 Pig

Hive 與 Pig 很適合在 HDInsight 中處理資料，但您有時需要更通用的語言。 Hive 與 Pig 都可讓您使用許多程式設計語言來建立使用者定義函數 (UDF)。 在本文中，您將了解如何從 Hive 和 Pig 中使用 Python UDF。

## <a name="requirements"></a>需求

* HDInsight 叢集

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 版或更新版本上唯一使用的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

* 文字編輯器

## <a name="python"></a>HDInsight 上的 Python

HDInsight 3.0 及更新版本的叢集上預設為已安裝 Python2.7。 Hive 可以搭配此版本的 Python 來進行串流處理 (Hive 與 Python 之間使用 STDOUT/STDIN 傳遞資料)。

HDInsight 也包含 Jython (以 Java 撰寫的 Python 實作)。 Pig 知道如何與 Jython 互動，而不必依賴串流，所以使用 Pig 時很適合。 您也可以使用標準的 Python (C Python) 來搭配 Pig。

## <a name="hivepython"></a>Hive 與 Python

從 Hive 中，透過 HiveQL **TRANSFORM** 陳述式，可將 Python 當做 UDF 使用。 例如，下列 HiveQL 會叫用儲存在 **streaming.py** 檔案中的 Python 指令碼。

**以 Linux 為基礎的 HDInsight**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**以 Windows 為基礎的 HDInsight**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> 在以 Windows 為基礎的 HDInsight 叢集上， **USING** 子句必須指定至 python.exe 的完整路徑。

以下是此範例所執行的動作：

1. 檔案開頭的 **add file** 陳述式將 **streaming.py** 檔案加入至分散式快取，供叢集中的所有節點存取。
2. **SELECT TRANSFORM ...使用**陳述式選取來自 **hivesampletable** 的資料。 它也會傳遞 clientid、devicemake 和 devicemodel 值至 **streaming.py** 指令碼。
3. **AS** 子句描述 **streaming.py** 傳回的欄位

<a name="streamingpy"></a> 以下是 HiveQL 範例使用的 **streaming.py** 檔案。

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

此指令碼會執行下列動作：

1. 從 STDIN 中讀取資料行。
2. 使用 `string.strip(line, "\n ")` 移除結尾新行字元。
3. 執行串流處理時，有一行包含所有的值，而每個值之間是一個定位字元。 因此， `string.split(line, "\t")` 可在每個索引標籤進行分割輸入，並只傳回欄位。
4. 處理完成時，輸出必須以一行寫入 STDOUT，而每一個欄位之間是一個定位字元。 這是使用  `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])` 以完成。
5. `while` 迴圈會一直重複直到沒有 `line` 讀取。

指令碼輸出是 `devicemake` 和 `devicemodel` 的輸入值串連，並且是串連值的雜湊。

請參閱 [執行範例](#running) ，以了解如何在 HDInsight 叢集上執行此範例。

## <a name="pigpython"></a>Pig 與 Python

從 Pig 中，透過 **GENERATE** 陳述式，可將 Python 指令碼當做 UDF 使用。 您可以使用 Jython 或 C Python 執行程式碼。

兩者之間的差異在於，Jython 是在 JVM 上執行，而且可以從 Pig 原生呼叫。 C Python 是外部處理序，因此 JVM 上的 Pig 所提供的資料會外送到 Python 處理序中執行的指令碼。 Python 指令碼的輸出會傳送回 Pig。

若要判斷 Pig 是使用 Jython 還是 C Python 來執行指令碼，請在從 Pig Latin 參考 Python 指令碼時使用 [註冊]。 下列範例使用 Pig 將指令碼註冊為 **myfuncs**：

* **若要使用 Jython**：`register '/path/to/pig_python.py' using jython as myfuncs;`
* **若要使用 C Python**：`register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> 在使用 Jython 時，pig_jython 檔案的路徑可以是本機路徑或 WASB:// 路徑。 不過，在使用 C Python 時，您必須參考您用來提交 Pig 作業之節點的本機檔案系統上的檔案。

通過註冊之後，此範例針對兩者的 Pig Latin 是相同的︰

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

以下是此範例所執行的動作：

1. 第一行將範例資料檔 **sample.log** 載入 **LOGS** 中。 它也會將每一筆記錄定義為 **chararray**。
2. 下一行會濾除任何 null 值，然後將操作的結果儲存至 **LOG**。
3. 接下來，逐一查看 **LOG** 中的記錄，並使用 **GENERATE** 叫用以 **myfuncs** 載入的 Python/Jython 指令碼所包含的 **create_structure** 方法。  **LINE** 用來將目前的記錄傳給函數。
4. 最後，使用 **DUMP** 指令將輸出傾印至 STDOUT。 這會在作業完成之後顯示結果。

C Python 與 Jython 的 Python 指令碼檔案很類似，唯一的差異在於，使用 C Python 時您必須從 **pig\_util** 匯入。 以下是 **pig\_python.py** 指令碼︰

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

> [!NOTE]
> 'pig_util' 不是您應該擔心的安裝項目；它會自動提供給指令碼。

先前，我們因為輸入沒有一致的結構描述而將 **LINE** 輸入定義為 chararray，記得嗎。 Python 指令碼會將資料轉換成一致的結構描述，以便輸出。

1. 檔案開頭的 **@outputSchema** 陳述式定義將傳回給 Pig 的資料格式。 在此案例中，這是一個 **data bag**(一種 Pig 資料類型)。 Bag 包含下列欄位，全部都是 chararray (字串)：
   
   * date - 記錄項目的建立日期
   * time - 記錄項目的建立時間
   * classname - 建立項目所針對的類別名稱
   * level - 記錄層級
   * detail - 記錄項目的詳細資料

2. 接下來，**def create_structure(input)** 定義可供 Pig 傳入行項目的函數。

3. 範例資料 **sample.log**大致上符合我們想要傳回的 date、time、classname、level 和 detail 結構描述。 但也有幾行是以字串 '*java.lang.Exception*' 開頭，需要修改為符合結構描述。 **if** 陳述式檢查這幾行，然後調整輸入資料將 '*java.lang.Exception*' 字串移至尾端，使資料符合我們預期的輸出結構描述。

4. 接下來，使用 **split** 命令，在前四個空白字元處分割資料。 輸出會指派到**日期**、**時間**、 **classname**、**層級**和**詳細資料**。

5. 最後，將值傳回給 Pig。

當資料傳回至 Pig 時，其將具有如同 **@outputSchema** 陳述式中定義的一致性結構描述，可將 Python 當做 UDF 使用。

## <a name="running"></a>執行範例
如果您使用以 Linux 為基礎的 HDInsight 叢集，請使用 **SSH** 步驟。 如果您使用以 Windows 為基礎的 HDInsight 叢集和 Windows 用戶端，請使用 **PowerShell** 步驟。

### <a name="ssh"></a>SSH
如需使用 SSH 的詳細資訊，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md) 或[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

1. 使用 Python 範例 [streaming.py](#streamingpy) 和 [pig_python.py](#jythonpy)，在開發機器上建立檔案的本機複本。

2. 使用 `scp` 將檔案複製到您的 HDInsight 叢集。 例如，下列命令會將檔案複製到名為 **mycluster**的叢集。
   
        scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. 使用 SSH 連接到叢集。 例如，下列會以使用者 **myuser** 的身分，連接到名為 **mycluster** 的叢集。
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
4. 從 SSH 工作階段，將先前上傳的 python 檔案加入叢集的 WASB 儲存體。
   
        hdfs dfs -put streaming.py /streaming.py
        hdfs dfs -put pig_python.py /pig_python.py

上傳檔案之後，請使用下列步驟執行 Hive 和 Pig 工作。

#### <a name="hive"></a>Hive

1. 使用 `hive` 命令啟動 Hive Shell。 在 Shell 載入完成時，您應會看到 `hive>` 提示。
2. 在出現 `hive>` 提示時輸入下列內容。
   
   ```hive
   add file wasbs:///streaming.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python streaming.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```
3. 輸入最後一行後，工作應該就會開始。 作業完成之後，它會傳回與下列範例類似的輸出：
   
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig"></a>Pig

1. 使用 `pig` 命令啟動 Shell。 在 Shell 載入完成時，您應會看到 `grunt>` 提示。

2. 在出現 `grunt>` 提示時輸入下列陳述式：
   
   ```pig
   Register wasbs:///pig_python.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. 輸入下列行之後，應該就會開始作業。 作業完成之後，它會傳回與下列類似的輸出。
   
        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. 使用 `quit` 結束 Grunt shell，然後使用下列命令編輯本機檔案系統上的 pig_python.py 檔案︰
   
    nano pig_python.py

5. 進入編輯器後，移除行首的 `#` 字元將以下這行取消註解：
   
        #from pig_util import outputSchema
   
    完成變更後，使用 Ctrl + X 結束編輯器。 選取 [Y]，然後按 Enter 儲存變更。

6. 使用 `pig` 命令再次啟動 Shell。 進入 `grunt>` 提示字元後，使用下列命令以使用 C Python 解譯器執行 Python 指令碼。
   
   ```pig
   Register 'pig_python.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```
   
    此作業完成後，您應該會看到和先前使用 Jython 執行指令碼時所得到的相同輸出。

### <a name="powershell"></a>PowerShell

這些步驟使用 Azure PowerShell。 如需使用 Azure PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

1. 使用 Python 範例 [streaming.py](#streamingpy) 和 [pig_python.py](#jythonpy)，在開發機器上建立檔案的本機複本。
2. 使用下列 PowerShell 指令碼，將 **streaming.py** 和 **pig\_python.py** 檔案上傳至伺服器。 在指令碼的前三行，替換您的 Azure HDInsight 叢集的名稱及 **streaming.py** 和 **pig\_python.py** 檔案的路徑。
   
   ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $pathToStreamingFile = "C:\path\to\streaming.py"
    $pathToJythonFile = "C:\path\to\pig_python.py"

    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    Set-AzureStorageBlobContent `
        -File $pathToStreamingFile `
        -Blob "streaming.py" `
        -Container $container `
        -Context $context

    Set-AzureStorageBlobContent `
        -File $pathToJythonFile `
        -Blob "pig_python.py" `
        -Container $container `
        -Context $context
   ```

    此指令碼會擷取 HDInsight 叢集的資訊，然後擷取預設儲存體帳戶的帳戶和金鑰，再將檔案上傳至容器的根目錄。
   
   > [!NOTE]
   > 關於其他的指令碼上傳方法，請參閱 [在 HDInsight 上將 Hadoop 工作的資料上傳](hdinsight-upload-data.md) 文件。

上傳檔案之後，請使用下列 PowerShell 指令碼來啟動工作。 當工作完成時，輸出應該會寫入至 PowerShell 主控台。

#### <a name="hive"></a>Hive

下列指令碼會執行 **streaming.py** 指令碼。 執行前，會提示您輸入您 HDInsight 叢集的 HTTPs/系統管理帳戶資訊。

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

# If using a Windows-based HDInsight cluster, change the USING statement to:
# "USING 'D:\Python27\python.exe streaming.py' AS " +
$HiveQuery = "add file wasbs:///streaming.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python streaming.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

$jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
    -Query $HiveQuery

$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds
Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds
# Uncomment the following to see stderr output
# Get-AzureRmHDInsightJobOutput `
#   -Clustername $clusterName `
#   -JobId $job.JobId `
#   -HttpCredential $creds `
#   -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

**Hive** 作業的輸出應該如下範例所示：

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

下列指令碼會使用 Jython 解譯器來使用 **pig_python.py** 指令碼。 執行前，會提示您輸入 HDInsight 叢集的 HTTPs/系統管理資訊。

> [!NOTE]
> 使用 PowerShell 遠端提交作業時，無法使用 C Python 做為解譯器。

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

$PigQuery = "Register wasbs:///pig_python.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

$jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds
# Uncomment the following to see stderr output
# Get-AzureRmHDInsightJobOutput `
#    -Clustername $clusterName `
#    -JobId $job.JobId `
#    -HttpCredential $creds `
#    -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

**Pig** 工作的輸出應該如下所示：

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>疑難排解

### <a name="errors-when-running-jobs"></a>執行工作時發生錯誤

執行 Hive 工作時，您可能會遇到類似以下的錯誤：

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

這個問題可能是由 streaming.py 檔案中的行尾結束符號所引起。 許多 Windows 編輯器預設都是使用 CRLF 做為行尾結束符號，但是 Linux 應用程式通常預期使用 LF。

若要在檔案上傳至 HDInsight 之前移除 CR 字元，您可以使用下列 PowerShell 陳述式︰

```powershell
$original_file ='c:\path\to\streaming.py'
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

### <a name="powershell-scripts"></a>PowerShell 指令碼

用來執行範例的兩個範例 PowerShell 指令碼都包含一行註解，此行會顯示作業的錯誤輸出。 如果您沒有看到預期的工作輸出，請將下列這一行取消註解，再查看錯誤資訊是否指出問題。

```powershell
# Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

錯誤資訊 (STDERR,) 和作業 (STDOUT,) 的結果也會記錄至您的 HDInsight 儲存體中。

| 關於此工作.. | 請在 Blob 容器中查看這些檔案 |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>接續步驟

如果您需要載入非預設提供的 Python 模組，請參閱 [如何將模組部署至 Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (英文)。

若要了解使用 MapReduce，及Pig、Hive 的其他使用方式，請參閱下列文件：

* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)


