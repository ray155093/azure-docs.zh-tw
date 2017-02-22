---
title: "從 PowerShell 使用 Mahout HDInsight 產生推薦 | Microsoft Docs"
description: "了解如何從您的用戶端上執行的 PowerShell 指令碼搭配 HDInsight (Hadoop) 使用 Apache Mahout 機器學習庫來產生電影推薦。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: fdca3ed832dc0895a04dc3fda2dcf863d8938d8a


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>透過在 HDInsight 上將 Apache Mahout 與 Hadoop 搭配使用來產生電影推薦 (PowerShell)
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

了解如何使用搭配 Azure HDInsight 的 [Apache Mahout](http://mahout.apache.org) 機器學習庫產生電影推薦。 本文件中，您會學習如何使用 Azure PowerShell 從遠端執行 Mahout。

Mahout 是 Apache Hadoop 的[機器學習服務][ml]程式庫。 Mahout 包含可處理資料的演算法，例如篩選、分類和叢集化。 在本文中，您會使用推薦引擎，以根據朋友看過的電影來產生電影推薦。

## <a name="prerequisites"></a>必要條件

* 以 Linux 為基礎的 HDInsight 叢集。 如需有關建立此叢集的資訊，請參閱[開始在 HDInsight 中使用 Linux 架構的 Hadoop][getstarted]。

> [!IMPORTANT]
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。

* **具有 Azure PowerShell 的工作站**。

    > [!IMPORTANT]
    > 使用 Azure Service Manager 管理 HDInsight 資源的 Azure PowerShell 支援已**被取代**，將會在 2017 年 1 月 1 日前移除。 本文件中的步驟會使用可與 Azure Resource Manager 搭配使用的新 HDInsight Cmdlet。
    >
    > 請遵循 [安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 中的步驟來安裝最新版的 Azure PowerShell。 如果您需要修改指令碼才能使用適用於 Azure Resource Manager 的新 Cmdlet，請參閱 [移轉至以 Azure Resource Manager 為基礎的開發工具 (適用於 HDInsight 叢集)](hdinsight-hadoop-development-using-azure-resource-manager.md) ，以取得詳細資訊。

## <a name="a-namerecommendationsagenerate-recommendations-by-using-azure-powershell"></a><a name="recommendations"></a>使用 Azure PowerShell 產生推薦

> [!NOTE]
> 雖然本節中使用的工作能夠利用 Azure PowerShell 來執行，但 Mahout 隨附的許多類別目前仍無法搭配 Azure PowerShell 運作，而必須使用 Hadoop 命令列來執行。 如需無法搭配 Azure PowerShell 使用的類別清單，請參閱 [疑難排解](#troubleshooting) 一節。
>
> 如需使用 SSH 連線到 HDInsight 並在叢集上直接執行 Mahout 範例的範例，請參閱[使用 Mahout 和 HDInsight (SSH) 來產生電影推薦](hdinsight-hadoop-mahout-linux-mac.md)。

Mahout 提供的其中一項功能是推薦引擎。 這個引擎接受 `userID``itemId` 和 `prefValue` (使用者偏好的項目) 格式的資料。 Mahout 接著可以執行共生分析來判斷：「偏好某項目的使用者同時也偏好其他這些項目」。 接著 Mahout 會以偏好的類似項目判斷使用者，並以此做出推薦。

以下使用電影做一個很簡單的範例：

* **共生**：Joe、Alice 和 Bob 都喜歡*《星際大戰》*、*《帝國大反擊》*和*《絕地大反攻》*。 Mahout 將判斷喜歡上述任何一部電影的使用者，也會喜歡另外兩部電影。

* **共生**：Bob 和 Alice 同時也喜歡*《威脅潛伏》*、*《複製人全面進攻》*和*《西斯大帝的復仇》*。 Mahout 將判斷喜歡前三部電影的使用者，也會喜歡這三部電影。

* **相似性推薦**：因為 Joe 喜歡前三部電影，Mahout 會查看具有相似偏好的其他使用者所喜歡但 Joe 還沒看過 (喜歡/評價) 的電影。 在此情況下，Mahout 將會推薦*《威脅潛伏》*、*《複製人全面進攻》*和*《西斯大帝的復仇》*。

### <a name="understanding-the-data"></a>了解資料

[GroupLens 研究][movielens]提供 Mahout 相容格式的電影評價資料，相當方便。 您可在位於 `/HdiSamples//HdiSamples/MahoutMovieData`的叢集預設儲存體取得這份資料。

有兩份檔案：`moviedb.txt` (影片相關資訊) 和 `user-ratings.txt`。 分析期間使用的是 user-ratings.txt 檔案，moviedb.txt 則是在顯示分析結果時用來提供使用者易懂的文字資訊。

user-ratings.txt 內包含的資料具有 `userID`、`movieID`、`userRating` 和 `timestamp` 結構，可告訴我們每位使用者對於影片的評價為何。 以下是資料範例：

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>執行工作

使用下列 Windows PowerShell 指令碼執行工作，以透過 Mahout 推薦引擎來處理影片資料：

> [!NOTE]
> 這個檔案會提示您輸入用來連接到 HDInsight 叢集並執行作業的資訊。 可能需要幾分鐘的時間來完成作業，並且下載 output.txt 檔案。

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
$creds=Get-Credential -Message "Enter the login for the cluster (the default name is usually 'admin')"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Use Hive to figure out the path to the mahout examples
#Because the file name/path has a version number in it that changes
$queryString = "!ls /usr/hdp/current/mahout-client"
$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString
$hiveJob=Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -HttpCredential $creds
$dummy = wait-azurermhdinsightjob -ClusterName $clusterName -JobId $hiveJob.JobId -HttpCredential $creds
#Get the files returned from Hive
$files=get-azurermhdinsightjoboutput -clustername $clusterName -JobId $hiveJob.JobId -DefaultContainer $container -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -HttpCredential $creds
#Find the file that starts with mahout-examples and ends in job.jar
$jarFile = $files | select-string "mahout-examples.+job\.jar" | % {$_.Matches.Value}
#Add the full path
$jarFile = "file:///usr/hdp/current/mahout-client/$jarFile"

# The arguments for the mahout job
# * input - the path to the data uploaded to HDInsight
# * output - the path to store output data
# * tempDir - the directory for temp files
$jobArguments = "-s", "SIMILARITY_COOCCURRENCE", `
                "--input", "/HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt",
                "--output", "/example/out",
                "--tempDir", "/example/temp"

# Create the job definition
$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
    -JarFile $jarFile `
    -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
    -Arguments $jobArguments

# Start the job
$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Wait on the job to complete
Write-Host "Wait for the job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds

# Write out any error information
Write-Host "STDERR"
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError

# Download the output
Get-AzureStorageBlobContent `
        -Blob example/out/part-r-00000 `
        -Container $container `
        -Destination output.txt `
        -Context $context
#Download movie and user files for use in displaying results
Get-AzureStorageBlobContent -blob "HdiSamples/HdiSamples/MahoutMovieData/moviedb.txt" `
        -Container $container `
        -Destination moviedb.txt `
        -Context $context
Get-AzureStorageBlobContent -blob "HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt" `
        -Container $container `
        -Destination user-ratings.txt `
        -Context $context
```

> [!NOTE]
> Mahout 工作不會移除處理工作時所建立的暫存資料。 範例工作中所指定的 `--tempDir` 參數會將暫存檔隔離到特定的目錄。

Mahout 工作不會將輸出傳回 STDOUT。 相反地，其會將該輸出儲存在指定的輸出目錄 **part-r-00000**中。 指令碼會下載這個檔案到您工作站上目前目錄的 **output.txt** 檔。

以下是此檔案的內容範例：

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

第一欄是 `userID`。 '[' 和 ']' 中包含的值是 `movieId`:`recommendationScore`。

指令碼也會下載使輸出格式更具可讀性所需的 `moviedb.txt` 和 `user-ratings.txt` 檔案。

### <a name="view-the-output"></a>檢視輸出

雖然產生的輸出可以在應用程式中使用，但非常難以讓人判讀。 來自伺服器的 `moviedb.txt`可用來解決電影名稱的 `movieId`。 使用下列 PowerShell 指令碼來顯示建議的影片名稱︰

```powershell
<#
.SYNOPSIS
    Displays recommendations for movies.
.DESCRIPTION
    Displays recommendations generated by Mahout
    with HDInsight example in a human readable format.
.EXAMPLE
    .\Show-Recommendation -userId 4
        -userDataFile "user-ratings.txt"
        -movieFile "moviedb.txt"
        -recommendationFile "output.txt"
#>

[CmdletBinding(SupportsShouldProcess = $true)]
param(
    #The user ID
    [Parameter(Mandatory = $true)]
    [String]$userId,

    [Parameter(Mandatory = $true)]
    [String]$userDataFile,

    [Parameter(Mandatory = $true)]
    [String]$movieFile,

    [Parameter(Mandatory = $true)]
    [String]$recommendationFile
)
# Read movie ID & description into hash table
Write-Host "Reading movies descriptions" -ForegroundColor Green
$movieById = @{}
foreach($line in Get-Content $movieFile)
{
    $tokens = $line.Split("|")
    $movieById[$tokens[0]] = $tokens[1]
}
# Load movies user has already seen (rated)
# into a hash table
Write-Host "Reading rated movies" -ForegroundColor Green
$ratedMovieIds = @{}
foreach($line in Get-Content $userDataFile)
{
    $tokens = $line.Split("`t")
    if($tokens[0] -eq $userId)
    {
        # Resolve the ID to the movie name
        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
    }
}
# Read recommendations generated by Mahout
Write-Host "Reading recommendations" -ForegroundColor Green
$recommendations = @{}
foreach($line in get-content $recommendationFile)
{
    $tokens = $line.Split("`t")
    if($tokens[0] -eq $userId)
    {
        #Trim leading/treailing [] and split at ,
        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
        foreach($movieIdAndScore in $movieIdAndScores)
        {
            #Split at : and store title and score in a hash table
            $idAndScore = $movieIdAndScore.Split(":")
            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
        }
        break
    }
}

Write-Host "Rated movies" -ForegroundColor Green
Write-Host "---------------------------" -ForegroundColor Green
$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                @{Expression={$_.Value};Label="Rating"}
$ratedMovieIds | format-table $ratedFormat
Write-Host "---------------------------" -ForegroundColor Green

write-host "Recommended movies" -ForegroundColor Green
Write-Host "---------------------------" -ForegroundColor Green
$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                        @{Expression={$_.Value};Label="Score"}
$recommendations | format-table $recommendationFormat
```

以下是執行此指令碼的範例：

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

輸出應該如下所示：

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="a-nametroubleshootingatroubleshooting"></a><a name="troubleshooting"></a>疑難排解

### <a name="cannot-overwrite-files"></a>無法覆寫檔案

Mahout 工作不會清除在處理期間所建立的暫存檔。 此外，工作也不會覆寫現有的輸出檔。

為了避免執行 Mahout 工作時發生錯誤，請在每次執行之前刪除暫存檔和輸出檔，或使用唯一的暫存和輸出目錄名稱。 使用下列 PowerShell 指令碼移除本文件中先前指令碼所建立的檔案︰

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

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard, 
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="a-namenopowershellaclasses-that-do-not-work-with-azure-powershell"></a><a name="nopowershell"></a>不適用於 Azure PowerShell 的類別

如果從 Windows PowerShell 中使用的 Mahout 工作利用到下列類別，則會傳回各種錯誤訊息：

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

若要執行用到這些類別的工作，請使用 SSH 連接至 HDInsight 叢集，然後從命令列執行作業。 如需使用 SSH 來執行 Mahout 作業的範例，請參閱[使用 Mahout 和 HDInsight (SSH) 來產生電影推薦](hdinsight-hadoop-mahout-linux-mac.md)。

## <a name="next-steps"></a>後續步驟

您現在已了解如何使用 Mahout，請繼續探索在 HDInsight 上使用資料的其他方法：

* [搭配 HDInsight 使用 Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools



<!--HONumber=Jan17_HO3-->


