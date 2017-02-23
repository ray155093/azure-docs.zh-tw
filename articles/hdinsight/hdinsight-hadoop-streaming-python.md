---
title: "使用 HDInsight 開發 Python MapReduce 工作 | Microsoft Docs"
description: "了解如何在 Linux 型 HDInsight 叢集上建立和執行 Python MapReduce 作業。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cf233279c0a26c3d2970596b1bb515508da20b89
ms.openlocfilehash: ad696f14d48452840805bc413d890309e523ce34


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>開發適用於 HDInsight 的 Python 串流程式

Hadoop 為 MapReduce 提供一個串流 API，可讓您以 Java 以外的語言撰寫 map 和 reduce 函數。 在本文中，您將學習如何使用 Python 來執行 MapReduce 作業。

本文是根據 Michael Noll 在 [使用 Python 撰寫 Hadoop MapReduce 程式](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/)所發佈的資訊和範例撰寫而成。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您需要下列項目。

* HDInsight 叢集上的 Linux 型 Hadoop

  > [!IMPORTANT]
  > 此文件中的步驟需要使用 Linux 的 HDInsight 叢集。 Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

* 文字編輯器
  
  > [!IMPORTANT]
  > 文字編輯器必須使用 LF 做為行尾結束符號。 如果使用 CRLF，在 Linux 型 HDInsight 叢集上執行 MapReduce 作業時會導致錯誤。 如果您不確定，請使用 [執行 MapReduce](#run-mapreduce) 一節中的選擇性步驟將所有 CRLF 轉換為 LF。

* **熟悉 SSH 和 SCP**。 如需搭配 HDInsight 使用 SSH 和 SCP 的詳細資訊，請參閱下列文章：
  
  * **Linux、Unix 或 OS X 用戶端**：請參閱 [從 Linux、OS X 或 Unix 在 HDInsight 上搭配使用 SSH 與 Linux 型 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

  * **Windows 用戶端**：請參閱 [從 Windows 在 HDInsight 上搭配使用 SSH 與 Linux 型 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="word-count"></a>字數統計

在此範例中，您將使用對應器和歸納器實作基本字數統計。 對應器會將句子拆解成個別文字，歸納器則會彙總文字和計數來產生輸出。

下列流程圖說明在對應和歸納階段期間所執行的程序。

![Map Reduce 的插圖](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python"></a>為何使用 Python？

Python 是一般用途的高階程式設計語言，可讓您以少於許多其他語言的程式碼行數快速表達概念。 資料科學家最近很喜歡用它做為原型語言，因為它的解譯本質、動態型別和簡潔語法讓它非常適合用於快速開發應用程式。

Python 會安裝在所有 HDInsight 叢集上。

## <a name="streaming-mapreduce"></a>串流 MapReduce

Hadoop 可讓您指定包含工作所使用的對應和歸納邏輯的檔案。 對應和歸納邏輯的特殊需求如下：

* **輸入**：對應和歸納元件必須從 STDIN 讀取輸入資料。
* **輸出**：對應和歸納元件必須將輸出資料寫入至 STDOUT。
* **資料格式**：所取用和產生的資料必須是機碼/值組，並以 Tab 字元隔開。

Python 可以使用 **sys** 模組從 STDIN 讀取資料並使用 **print** 來列印到 STDOUT，輕鬆應付這些需求。 剩下的工作便只需要在機碼和值之間以 Tab (`\t`) 字元格式化資料。

## <a name="create-the-mapper-and-reducer"></a>建立對應器和歸納器

對應器和歸納器是文字檔案，在此案例中為 **mapper.py** 和 **reducer.py** (以便清楚知道各自的功用)。 您可以使用自行選擇的編輯器建立這些檔案。

### <a name="mapperpy"></a>Mapper.py

建立名為 **mapper.py** 的新檔案並使用下列程式碼做為內容：

```python
#!/usr/bin/env python

# Use the sys module
import sys

# 'file' in this case is STDIN
def read_input(file):
    # Split each line into words
    for line in file:
        yield line.split()

def main(separator='\t'):
    # Read the data using read_input
    data = read_input(sys.stdin)
    # Process each words returned from read_input
    for words in data:
        # Process each word
        for word in words:
            # Write to STDOUT
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```

請花一點時間仔細閱讀程式碼，以便了解它的功用。

### <a name="reducerpy"></a>reducer.py

建立名為 **reducer.py** 的新檔案並使用下列程式碼做為內容：

```python
#!/usr/bin/env python

# import modules
from itertools import groupby
from operator import itemgetter
import sys

# 'file' in this case is STDIN
def read_mapper_output(file, separator='\t'):
    # Go through each line
    for line in file:
        # Strip out the separator character
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # Read the data using read_mapper_output
    data = read_mapper_output(sys.stdin, separator=separator)
    # Group words and counts into 'group'
    #   Since MapReduce is a distributed process, each word
    #   may have multiple counts. 'group' will have all counts
    #   which can be retrieved using the word as the key.
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            # For each word, pull the count(s) for the word
            #   from 'group' and create a total count
            total_count = sum(int(count) for current_word, count in group)
            # Write to stdout
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # Count was not a number, so do nothing
            pass

if __name__ == "__main__":
    main()
```

## <a name="upload-the-files"></a>上傳檔案

**mapper.py** 和 **reducer.py** 必須同時位於叢集的前端節點上才能加以執行。 本節提供可用來將檔案上傳至叢集的範例 `scp` 命令與 Azure PowerShell 指令碼。

> [!IMPORTANT]
> 使用 `scp` 或 PowerShell 來上傳檔案，這兩者之間有重要的差異：
>
> * 使用 `scp` 會將檔案放置在叢集的主要前端節點上。 這假設您稍後將會從 SSH 工作階段連線到前端節點並執行作業。
> * 使用 PowerShell 指令碼會將檔案放置到叢集的預設儲存體。 這假設您稍後將使用 PowerShell 指令碼從遠端用戶端執行作業。

### <a name="upload-using-scp"></a>使用 SCP 上傳

從開發環境上與 **mapper.py** 和 **reducer.py** 相同的目錄中，使用下列命令。 以叢集的 SSH 使用者取代 **username** ，並以叢集的名稱取代 **clustername**。

`scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

這樣就會將檔案從本機系統複製到前端節點。

> [!NOTE]
> 如果您使用密碼保護 SSH 帳戶，系統會提示您輸入密碼。 如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑，例如 `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`。

### <a name="upload-using-powershell"></a>使用 PowerShell 上傳

1. 在開發環境中建立名稱為 `Put-FilesInHDInsight.ps1` 的新檔案，並使用下列程式碼作為檔案的內容：

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source,
        [Parameter(Mandatory = $true)]
        [String]$destination
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and upload the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Upload the file
            Set-AzureStorageBlobContent `
                -File $source `
                -Blob $destination `
                -Container $storageContainer `
                -Context $context
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Upload the file. Prepend the destination with the cluster root
            Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $source -Destination "$clusterRoot$destination"
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. 若要使用此指令碼來上傳檔案，請在 Azure PowerShell 提示字元中使用下列程式碼︰

    `.\Put-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source mapper.py -destination mapper.py`

    如果出現提示，請輸入叢集的 HTTPS 登入認證。

3. 重複執行命令，將 `mapper.py` 取代為 `reducer.py`。 這會將對應器和歸納器的檔案上傳至叢集的預設儲存體。

## <a name="run-mapreduce-ssh"></a>執行 MapReduce (SSH)

使用下列步驟，從 SSH 工作階段連線到叢集並執行串流 MapReduce 作業。

1. 使用 SSH 連線到叢集：
   
   `ssh username@clustername-ssh.azurehdinsight.net`
   
   > [!NOTE]
   > 如果您使用密碼保護 SSH 帳戶，系統會提示您輸入密碼。 如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑，例如 `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`。

2. (選擇性) 如果您建立 mapper.py 和 reducer.py 檔案時使用的文字編輯器是以 CRLF 做為行尾結束符號，或者，您不知道您的編輯器使用哪種行尾結束符號，請使用下列命令將 mapper.py 和 reducer.py 中出現的 CRLF 轉換為 LF。
   
    `perl -pi -e 's/\r\n/\n/g' mappery.py`
    `perl -pi -e 's/\r\n/\n/g' reducer.py`

3. 使用下列命令啟動 MapReduce 工作。
   
    `yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout`
   
    此命令有下列幾個部分：
   
   * **hadoop-streaming.jar**：執行串流 MapReduce 作業時使用。 它能連結 Hadoop 和您提供的外部 MapReduce 程式碼。

   * **-files**：告訴 Hadoop 此 MapReduce 工作需要指定的檔案，而且應複製到所有背景工作節點。

   * **-mapper**：告訴 Hadoop 要做為對應器的檔案。

   * **-reducer**：告訴 Hadoop 要做為歸納器的檔案。

   * **-input**：要從中計算字數的輸入檔案。

   * **-output**：要將輸出寫入到的目錄。
     
     > [!NOTE]
     > 該工作會建立此目錄。

作業啟動後您會看到一堆 **INFO** 陳述式，最後便會看到以百分比顯示的 **map** 和 **reduce** 作業。

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

工作完成後，您會收到工作的狀態資訊。

## <a name="run-mapreduce-powershell"></a>執行 MapReduce (PowerShell)

在開發環境中執行下列步驟，從 PowerShell 執行串流 MapReduce。 PowerShell 指令碼會透過連線至使用 WebHCat 的 HDInsight 叢集來執行作業。

1. 建立名稱為 `Start-HDInsightStreamingPythonJob` 的新檔案，並使用下列程式碼作為檔案的內容：

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$inputPath,
        [Parameter(Mandatory = $true)]
        [String]$outputPath
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the login (HTTPS) credentials for the cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # Create the streaming job definition
    # Note: This assumes that the mapper.py and reducer.py
    #       are in the root of default storage. If you put them in a 
    #       subdirectory, change the -Files parameter to the correct path.
    $jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
        -Files "/mapper.py", "/reducer.py" `
        -Mapper "mapper.py" `
        -Reducer "reducer.py" `
        -InputPath $inputPath `
        -OutputPath $outputPath

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait for the job to complete
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    ```

2. 在 Azure PowerShell 提示字元中使用下列程式碼來執行作業︰

    `.\Start-HDInsightStreamingPythonJob.ps1 -clusterName <your HDInsight cluster name> -inputPath "/example/data/gutenberg/davinci.txt" -outputPath "/example/wordcountout"`

    這將使用之前上傳到叢集的 `mapper.py` 和 `reducer.py` 來計算 `davinci.txt` 檔案中的字數。 輸出會儲存在叢集之預設儲存體上的 `/example/wordcount` 資料夾。

    作業完成時，會顯示如下資訊：

    ```
    Cluster         : myhdicluster
    HttpEndpoint    : myhdicluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1486046226168_0004
    ParentId        :
    PercentComplete : map 100% reduce 100%
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done
    StatusFolder    : 2017-02-06T19-14-28-a3dda3ca-f489-4287-afc9-b5e16e2e7c7a
    ```

## <a name="view-the-output"></a>檢視輸出

作業的輸出會儲存在 `/example/wordcountout` 目錄。 您可以從 SSH 工作階段檢視此內容，或是將它下載至本機然後從 PowerShell 檢視它。

若要從連往叢集的 SSH 工作階段檢視該資料，請使用下列命令：

`hdfs dfs -text /example/wordcountout/part-00000`

這會顯示文字及文字出現次數的清單。 以下是輸出資料的範例：

```
wrenching       1
wretched        6
wriggling       1
wrinkled,       1
wrinkles        2
wrinkling       2
```

若要從開發環境使用 PowerShell 檢視輸出，請使用下列步驟︰

1. 建立名稱為 `Get-FilesInHDInsight.ps1` 的新檔案，並使用下列程式碼作為檔案的內容：

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and download the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Download the file
            Get-AzureStorageBlobContent `
                -Container $storageContainer `
                -Blob $source `
                -Context $context `
                -Destination "./output.txt"
            # Display the output
            Get-Content "./output.txt"
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Download the file. Prepend the destination with the cluster root
            # NOTE: Unlike getting a blob, this just gets the content and no
            #       file is created locally.
            Get-AzureRmDataLakeStoreItemContent -Account $dataLakeStoreName -Path $clusterRoot$source -Confirm
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. 在 Azure PowerShell 提示字元中，使用下列程式碼來執行指令碼並檢視輸出︰

    `Get-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source "example/wordcountout/part-00000"`

    這會顯示文字及文字出現次數的清單。 以下是輸出資料的範例：

    ```
    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2
    ```

## <a name="next-steps"></a>後續步驟

現在您已學會如何搭配 HDInsight 使用串流 MapRedcue 工作，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)




<!--HONumber=Feb17_HO1-->


