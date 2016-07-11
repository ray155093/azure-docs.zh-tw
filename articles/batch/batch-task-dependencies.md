<properties
	pageTitle="Azure Batch 中的工作相依性 | Microsoft Azure"
	description="建立相依於其他工作順利完成的工作，以便在 Azure Batch 中處理 MapReduce 樣式和類似的巨量資料工作負載。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/29/2016"
	ms.author="marsma" />

# Azure Batch 中的工作相依性

如果您想要處理雲端中的 MapReduce 樣式計算工作負載，而且您擁有其工作可表示為有向非循環圖 (DAG) 的資料處理作業，或是下游工作相依於上游工作輸出的其他任何作業，則 Azure Batch 的工作相依性功能將會是您的解決方案。

這項功能可讓您建立工作，並讓工作只在一或多個其他工作順利完成後，才在計算節點上排定執行。例如，您可以建立一項作業，透過不同的平行工作呈現 3D 電影的每個畫面，並讓此作業的最後一項工作 (「合併工作」) 只在順利呈現所有畫面後，才將呈現的畫面合併成為完整電影。

您可以建立以一對一或一對多關聯性相依於其他工作的工作，或甚至是具有範圍相依性的工作，後面這種工作會相依於特定工作識別碼範圍內的一組工作的順利完成。您可以結合這三種基本案例來建立多對多關聯性。

## Batch .NET 的工作相依性

本文將討論使用 [Batch .NET][net_msdn] 程式庫設定工作相依性。我們會先告訴您如何對作業[啟用工作相依性](#enable-task-dependency)，然後簡單地示範如何[設定工作的相依性](#create-dependent-tasks)。最後，我們將討論 Batch 支援的[相依性案例](#dependency-scenarios)。

## 啟用工作相依性

若要在 Batch 應用程式中使用工作相依性，您必須先讓 Batch 服務知道作業將會使用工作相依性。在 Batch .NET 中，將作業的 [UsesTaskDependencies][net_usestaskdependencies] 屬性設定為 `true`，以對 [CloudJob][net_cloudjob] 啟用工作相依性：

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

在上述程式碼片段中，"batchClient" 是 [BatchClient][net_batchclient] 類別的執行個體。

## 建立相依的工作

若要建立相依於一或多個其他工作順利完成的工作，您必須讓 Batch 知道此工作「相依於」其他工作。在 Batch .NET 中，設定執行個體為 [TaskDependencies][net_taskdependencies] 類別的 [CloudTask][net_cloudtask].[DependsOn][net_dependson] 屬性︰

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

此程式碼片段會建立識別碼為 "Flowers" 的工作，並讓此工作只在識別碼為 "Rain" 和 "Sun" 的工作順利完成後，才在計算節點上排定執行。

 > [AZURE.NOTE] 當工作處於**已完成**狀態且其**結束代碼** 是 `0` 時，才會將其視為已順利完成。在 Batch .NET 中，這表示 [CloudTask][net_cloudtask].[State][net_taskstate] 屬性值為 `Completed`，而 CloudTask 的 [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] 屬性值為 `0`。

## 相依性案例

Azure Batch 中可使用的基本工作相依性案例有三種︰一對一、一對多、工作識別碼範圍相依性。結合這三種案例即可提供第四種案例，也就是多對多。

 案例&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 範例 | |
 :-------------------: | ------------------- | -------------------
 [一對一](#one-to-one) | 「taskB」相依於「taskA」 <p/>「taskB」要等到「taskA」順利完成後，才會排定執行。 | ![圖表︰一對一工作相依性][1]
 [一對多](#one-to-many) | 「taskC」相依於「taskA」和「taskB」 <p/>「taskC」要等到「taskA」和「taskB」順利完成後，才會排定執行。 | ![圖表︰一對多工作相依性][2]
 [工作識別碼範圍](#task-id-range) | 「taskD」相依於某一範圍的工作 <p/>「taskD」要等到識別碼「1」到「10」的工作順利完成後，才會排定執行。 | ![圖表︰工作識別碼範圍相依性][3]

>[AZURE.TIP] 您可以建立**多對多**關聯性，例如工作 C、D、E 和 F 均相依於工作 A 和 B。舉例來說，在下游工作相依於多個上游工作輸出的平行前置處理案例中，這種關聯性就很有用。

## 一對一

若要建立一項工作，而該工作相依於一項順利完成的其他工作，您可以在填入 [CloudTask][net_cloudtask] 的 [DependsOn][net_dependson] 屬性時，對 [TaskDependencies][net_taskdependencies].[OnId][net_onid] 靜態方法提供單一工作識別碼。

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

## 一對多

若要建立一項工作，且該工作相依於多項順利完成的工作，您可以在填入 [CloudTask][net_cloudtask] 的 [DependsOn][net_dependson] 屬性時，對 [TaskDependencies][net_taskdependencies].[OnId][net_onids] 靜態方法提供一組工作識別碼。

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

## 工作識別碼範圍

若要建立一項工作，而該工作相依於一組識別碼位於某範圍內且順利完成的工作，您可以在填入 [CloudTask][net_cloudtask] 的 [DependsOn][net_dependson] 屬性時，對 [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] 靜態方法提供該範圍的第一個和最後一個工作識別碼。

>[AZURE.IMPORTANT] 對相依性使用工作識別碼範圍時，該範圍內的工作識別碼「必須」是**以字串表示的****整數值**。此外，**範圍內的每項工作**皆必須順利完成，才能排定執行相依工作。

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## 程式碼範例

[TaskDependencies][github_taskdependencies] 範例專案是 GitHub 上的其中一個 [Azure Batch 程式碼範例][github_samples]。此 Visual Studio 2015 方案會示範如何啟用作業的工作相依性、建立相依於其他工作的工作，並在計算節點集區上執行這些工作。

## 後續步驟

### 應用程式部署

Batch 的[應用程式封裝](batch-application-packages.md)功能提供了簡單的方法，供您部署您的工作在計算節點上執行的應用程式並設定其版本。

### 安裝應用程式和預備資料

請查看 Azure Batch 論壇中的[在 Batch 計算節點安裝應用程式和預備資料][forum_post]文章，以取得準備節點以執行工作的各種方法概觀。這篇文章是由 Azure Batch 小組的其中一名成員所撰寫，非常適合做為入門指南，以讓您了解將檔案 (包括應用程式和工作的輸入資料) 放到計算節點的不同方法，以及每個方法所應納入的一些特殊考量。

[forum_post]: https://social.msdn.microsoft.com/Forums/zh-TW/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "圖表︰一對一相依性"
[2]: ./media/batch-task-dependency/02_one_to_many.png "圖表︰一對多相依性"
[3]: ./media/batch-task-dependency/03_task_id_range.png "圖表︰工作識別碼範圍相依性"

<!---HONumber=AcomDC_0629_2016-->