---
title: "將作業和工作輸出保存到 Azure 儲存體 - Azure Batch | Microsoft Docs"
description: "了解如何將 Azure 儲存體做為 Batch 工作和作業輸出的永久性存放區，並在 Azure 入口網站中啟用已保存輸出的檢視。"
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 3b3aa18eb52993843be1feeb8e0b2a43339413c3
ms.lasthandoff: 03/09/2017


---
# <a name="persist-results-from-completed-jobs-and-tasks-to-azure-storage"></a>將已完成之作業和工作的結果保存到 Azure 儲存體

您在 Batch 中執行的工作通常會產生必須儲存的輸出，並在後續由作業中的其他工作、執行該作業的用戶端應用程式 (或兩者) 擷取。 此輸出可能是透過處理與工作執行相關的輸入資料或記錄檔，而建立的檔案。 本文將介紹一個 .NET 類別庫，它使用以慣例為基礎的技術將工作輸出保存在 Azure Blob 儲存體，即使您刪除集區、作業和計算節點之後，仍然可以存取這些輸出。

藉由使用本文中的技術，您也可以在 [Azure 入口網站][portal]內的 [已儲存的輸出檔案] 和 [已儲存的記錄檔] 中檢視您的工作輸出。

![入口網站中的 [已儲存的輸出檔案] 和 [已儲存的記錄檔] 選取器][1]

> [!NOTE]
> 本文所討論的 [Azure Batch 檔案慣例][nuget_package] .NET 類別庫目前僅供預覽。 在公開上市之前，此處所述的某些功能可能會變更。
> 
> 

## <a name="task-output-considerations"></a>工作輸出的考量
設計 Batch 方案的時候，您必須考慮與作業和工作輸出相關的幾項因素。

* **計算節點存留期**：計算節點通常是過渡性的，尤其是在啟用自動調整的集區中。 只有當節點存在時，才能取得在該節點上執行的工作輸出，且僅能在您針對該工作所設定的保留期內才能取得。 為了確保工作輸出的保存，您的工作必須將其輸出檔案上傳到永久性存放區 (如 Azure 儲存體)。
* **輸出儲存體**：若要將工作輸出資料保存在永久性儲存體，您可以在工作程式碼中使用 [Azure 儲存體 SDK](../storage/storage-dotnet-how-to-use-blobs.md) ，來將工作輸出上傳到 Blob 儲存體容器。 如果您實作容器和檔案命名慣例，您的用戶端應用程式，或作業中的其他工作就能根據慣例來找出並下載這些輸出。
* **輸出擷取**：如果您的工作有保存其輸出，即可直接從集區中的計算節點，或 Azure 儲存體擷取工作輸出。 若要直接從計算節點擷取工作的輸出，您需要檔案名稱和該檔案在節點上的輸出位置。 如果您將輸出保存在 Azure 儲存體，則下游工作或您的用戶端應用程式，必須有該檔案在 Azure 儲存體中的完整路徑，才能使用 Azure 儲存體 SDK 下載它。
* **檢視輸出**：當您在 Azure 入口網站中瀏覽至 Batch 工作並選取 [節點上的檔案] 時，系統將會顯示與該工作相關聯的所有檔案，而不只是您感興趣的輸出檔案。 同樣地，只有當運算節點存在時，且僅在您針對該工作設定的保留期內，才能取得該節點的檔案。 若要在入口網站或 [Azure 儲存體總管][storage_explorer]等應用程式中，檢視已保存在 Azure 儲存體的工作輸出，您必須知道其位置並直接瀏覽至該檔案。

## <a name="help-for-persisted-output"></a>已保存輸出的說明
為了協助您更容易保存作業和工作的輸出，Batch 小組已經定義並實作一組命名慣例，以及一個 .NET 類別庫 ([Azure Batch 檔案慣例][nuget_package]庫)，以供您用於您的 Batch 應用程式。 此外，Azure 入口網站能辨識這些命名慣例，讓您可以輕鬆找到使用該程式庫所儲存的檔案。

## <a name="using-the-file-conventions-library"></a>使用檔案慣例庫
[Azure Batch 檔案慣例][nuget_package]為 .NET 類別庫，您的 Batch .NET 應用程式可使用它輕鬆地將工作輸出儲存到 Azure 儲存體，或從其中擷取工作輸出。 它可用於工作和用戶端程式碼 -- 在工作程式碼中用來保存檔案，並在用戶端程式碼中用來列出和擷取檔案。 您的工作也可以將程式庫用於擷取上游工作的輸出，例如 [工作相依性](batch-task-dependencies.md) 案例。

慣例庫會負責確保儲存體容器和工作輸出檔案皆按照慣例命名，並會在保存到 Azure 儲存體時上傳至正確的位置。 擷取輸出的時候，您可以透過依識別碼和用途來列出或擷取輸出，來輕鬆地找到特定作業或工作的輸出，而不需要知道檔案名稱或是該檔案在儲存體中的位置。

例如，您可以使用程式庫來「列出工作 7 的所有中繼檔案」或「取得 *mymovie*作業的預覽縮圖」，而不需要知道該檔案在您儲存體帳戶中的名稱或位置。

### <a name="get-the-library"></a>取得程式庫
您可以從 [NuGet][nuget_package] 取得此程式庫，其中包含新的類別，並透過新方法延伸 [CloudJob][net_cloudjob] 和 [CloudTask][net_cloudtask] 類別。 您可以使用 [NuGet 程式庫套件管理員][nuget_manager]將它新增到您的 Visual Studio 專案。

> [!TIP]
> 您可以在 GitHub 上的 Microsoft Azure SDK for .NET 儲存機制中，找到 Azure Batch 檔案慣例庫的[原始程式碼][github_file_conventions]。
> 
> 

## <a name="requirement-linked-storage-account"></a>必要條件：已連結的儲存體帳戶
若要使用檔案慣例庫將輸出儲存到永久性儲存體，並在 Azure 入口網站中檢視它們，您必須 [連結 Azure 儲存體帳戶連結](batch-application-packages.md#link-a-storage-account) 到您的 Batch 帳戶。 如果您尚未這麼做，請使用 Azure 入口網站將儲存體帳戶連結到您的 Batch 帳戶：

[Batch 帳戶] 刀鋒視窗 > [設定] > [儲存體帳戶] > [儲存體帳戶 (無)] > 選取您訂用帳戶中的儲存體帳戶

如需連結儲存體帳戶的詳細逐步解說，請參閱 [使用 Azure Batch 應用程式封裝部署應用程式](batch-application-packages.md)。

## <a name="persist-output"></a>保存輸出
使用檔案慣例庫來儲存作業和工作輸出時，須執行兩個主要動作：建立儲存體容器，以及將輸出儲存到容器。

> [!WARNING]
> 由於所有的作業和工作輸出都儲存在同一個容器，因此，如果同時有大量的工作嘗試要保存檔案，系統可能會強制執行 [儲存體節流限制](../storage/storage-performance-checklist.md#blobs) 。
> 
> 

### <a name="create-storage-container"></a>建立儲存體容器
在您的工作開始將輸出保存到儲存體之前，您必須先建立 Blob 儲存體容器，讓工作能上傳其輸出。 呼叫 [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync] 來執行此動作。 這個擴充方法採用 [CloudStorageAccount][net_cloudstorageaccount] 物件做為參數，並且會建立容器，該容器的命名方式將會確保其內容可由 Azure 入口網站，以及本文稍後將討論的擷取方式進行探索。

您通常會將此程式碼置於您的用戶端應用程式 -- 也就是建立您的集區、作業及工作的應用程式。

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>儲存工作輸出
當您在 Blob 儲存體中備妥容器之後，工作便可以使用檔案慣例程式庫中的 [TaskOutputStorage][net_taskoutputstorage] 類別來將輸出儲存到容器。

在您的工作程式碼中，先建立一個 [TaskOutputStorage][net_taskoutputstorage] 物件，然後在工作完成時呼叫 [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync] 方法，以將其輸出儲存到 Azure 儲存體。

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

"output kind" (輸出類型) 參數會用來分類保存的檔案。 一共有四種預先定義的 [TaskOutputKind][net_taskoutputkind] 類型："TaskOutput"、"TaskPreview"、"TaskLog" 和 "TaskIntermediate"。 您也可以自行定義對您的工作流程有幫助的自訂類型。

這些輸出類型可供您在稍後針對特定工作的保存輸出查詢 Batch 時，指定要列出的輸出類型。 換句話說，當您列出某個工作的輸出時，可以根據其中一個輸出類型來篩選清單。 例如，「給我工作 *109* 的 *preview* 輸出」。 本文稍後的 [擷取輸出](#retrieve-output) 會提供列出和擷取輸出的詳細資訊。

> [!TIP]
> 輸出類型也會指定特定檔案出現在 Azure 入口網站的位置：*TaskOutput* 類的檔案會出現在 [工作輸出檔案] 中，而 *TaskLog* 檔案會出現在 [工作記錄] 中。
> 
> 

### <a name="store-job-outputs"></a>儲存工作輸出
除了儲存工作輸出，您也可以儲存和整個作業相關聯的輸出。 例如，在影片轉譯作業的合併工作中，您可以將完整轉譯的影片以作業輸出的形式保存。 當您的作業完成時，用戶端應用程式即可列出並擷取該作業的輸出，而不需要查詢個別的工作。

呼叫 [JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] 方法，並指定 [JobOutputKind][net_joboutputkind] 和檔案名稱以儲存作業輸出：

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

如同工作輸出的 TaskOutputKind，您會使用 [JobOutputKind][net_joboutputkind] 參數來分類作業的保存檔案。 此參數可讓您在稍後查詢 (列出) 特定的輸出類型。 JobOutputKind 包含了輸出和預覽類型，且支援建立自訂類型。

### <a name="store-task-logs"></a>儲存工作記錄檔
除了在工作或作業完成時將檔案保存到永久性儲存體之外，您可能也會需要保存在工作執行期間更新的檔案 -- 例如記錄檔或 `stdout.txt` 和 `stderr.txt`。 針對此目的，Azure Batch 檔案慣例庫會提供 [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync] 方法。 透過 [SaveTrackedAsync][net_savetrackedasync]，您可以追蹤節點上檔案的更新 (依照您指定的時間間隔)，並將這些更新保存到 Azure 儲存體。

在下列程式碼片段中，我們使用 [SaveTrackedAsync][net_savetrackedasync]，於工作執行期間每 15 秒更新一次 Azure 儲存體中的 `stdout.txt`：

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)` 為您的工作正常會執行的程式碼預留位置。 例如，您可能有程式碼會從 Azure 儲存體下載資料，並對這些資料執行轉換或計算。 此程式碼片段的重點，在於示範如何將這樣的程式碼用 `using` 區塊包裝，以透過 [SaveTrackedAsync][net_savetrackedasync] 定期更新檔案。

`Task.Delay` 必須位於此 `using` 區塊的結尾，以確保節點代理程式有時間將標準輸出的內容排清至節點上的 stdout.txt 檔案 (節點代理程式是在集區中的每個節點上執行，並在節點和 Batch 服務之間提供命令和控制介面的程式)。 若沒有此延遲，就可能會遺漏輸出的最後幾秒。 此延遲可能並非所有檔案都需要。

> [!NOTE]
> 當您使用 SaveTrackedAsync 啟用檔案追蹤時，只有*附加*到追蹤檔案的資料會保存到 Azure 儲存體。 請只將此方法用於追蹤非輪替記錄檔，或其他所附加的檔案 (也就是說，資料只有在更新時才會附加到檔案結尾)。
> 
> 

## <a name="retrieve-output"></a>擷取輸出
當您使用 Azure Batch 檔案慣例庫擷取保存的輸出時，您是以工作和作業為主的方式進行。 您可以要求指定工作或作業的輸出，而不需要知道它在 Blob 儲存體中的位置，甚至連檔案名稱也不需要。 您可以直接說：「給我工作 *109*的輸出檔案」。

下列程式碼片段會逐一查看作業的所有工作，並顯示該工作輸出檔案的相關資訊，然後從儲存體下載該工作的檔案。

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>工作輸出與 Azure 入口網站
Azure 入口網站會顯示使用 [Azure Batch 檔案慣例讀我檔案][github_file_conventions_readme]中所示的命名慣例，保存到已連結 Azure 儲存體帳戶的工作輸出和記錄。 您可以使用自選的語言實作這些慣例，或是使用 .NET 應用程式中的檔案慣例庫。

### <a name="enable-portal-display"></a>啟用入口網站顯示
若要讓您的輸出顯示在入口網站中，您必須滿足下列需求：

1. [連結 Azure 儲存體帳戶](#requirement-linked-storage-account) 到您的 Batch 帳戶。
2. 保存輸出時，依照預先定義的儲存體容器命名與檔案命名慣例。 您可以在檔案慣例庫的[讀我檔案][github_file_conventions_readme]中找到這些慣例的定義。 如果您使用 [Azure Batch 檔案慣例][nuget_package]庫來保存輸出，即符合此要求。

### <a name="view-outputs-in-the-portal"></a>在入口網站中檢視輸出
若要在 Azure 入口網站中檢視工作輸出和記錄，請瀏覽到您對其輸出有興趣的工作，然後按一下 [已儲存的輸出檔案] 或 [已儲存的記錄檔]。 此影像顯示識別碼為 "007" 之工作的 [已儲存的輸出檔案]  ：

![Azure 入口網站中的 [工作輸出] 刀鋒視窗][2]

## <a name="code-sample"></a>程式碼範例
[PersistOutputs][github_persistoutputs] 範例專案是 GitHub 上的其中一個 [Azure Batch 程式碼範例][github_samples]。 此 Visual Studio 解決方案示範如何使用 Azure Batch 檔案慣例庫，將工作輸出保存到永久性儲存體。 若要執行範例，請遵循下列步驟：

1. 在 **Visual Studio 2015 或更新版本**中開啟專案。
2. 將您 Batch 和儲存體的**帳戶認證**新增到 Microsoft.Azure.Batch.Samples.Common 專案中的 **AccountSettings.settings**。
3.  (但不要執行) 該解決方案。 如果出現提示，請還原任何 NuGet 封裝。
4. 使用 Azure 入口網站來為 [PersistOutputsTask](batch-application-packages.md) 上傳 **應用程式封裝**。 將 `PersistOutputsTask.exe` 及其相依性組件包含在 .zip 封裝中，將應用程式識別碼和應用程式封裝版本分別設為 "PersistOutputsTask" 和 "1.0"。
5. **啟動** (執行) **PersistOutputs** 專案。

## <a name="next-steps"></a>後續步驟
### <a name="application-deployment"></a>應用程式部署
Batch 的 [應用程式封裝](batch-application-packages.md) 功能提供了簡單的方法，供您部署您的工作在計算節點上執行的應用程式並設定其版本。

### <a name="installing-applications-and-staging-data"></a>安裝應用程式和預備資料
請查看 Azure Batch 論壇中的 [Installing applications and staging data on Batch compute nodes (在 Batch 計算節點安裝應用程式和預備資料)][forum_post] 文章，以取得準備節點以執行工作的各種方法概觀。 這篇文章是由 Azure Batch 小組的其中一名成員所撰寫，非常適合做為入門指南，以讓您了解將檔案 (包括應用程式和工作的輸入資料) 放到計算節點的不同方法，以及每個方法的一些特殊考量。

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "入口網站中的 [已儲存的輸出檔案] 和 [已儲存的記錄檔] 選取器"
[2]: ./media/batch-task-output/task-output-02.png "Azure 入口網站中的 [工作輸出] 刀鋒視窗"

