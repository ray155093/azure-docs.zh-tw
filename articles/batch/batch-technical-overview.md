---
title: "Azure Batch 服務基本概念 | Microsoft Docs"
description: "了解如何將 Azure Batch 服務用於大規模的平行工作負載和 HPC 工作負載 "
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 6f05aa3f06c8ec86d58cafd406c2752ddb2eecc1
ms.openlocfilehash: f3429fff9362451e687435e0c5e779feda564e46


---
# <a name="basics-of-azure-batch"></a>Azure Batch 的基本概念
Azure Batch 是一項平台服務，可用於在雲端有效地執行大規模的平行和高效能運算 (HPC) 應用程式。 Azure Batch 可排程要在一組受管理的虛擬機器上執行的計算密集型工作，而且可以調整計算資源以符合工作的需求。

使用 Batch 時，您可以輕鬆定義用來大規模平行執行應用程式的 Azure 計算資源。 不需要手動建立、設定和管理 HPC 叢集、個別的虛擬機器、虛擬網路或複雜的作業和工作排程基礎結構。 Azure Batch 會為您自動執行或簡化這些工作。

## <a name="use-cases-for-batch"></a>Batch 的使用案例
Batch 是一項受管理的 Azure 服務，可用於批次處理或批次運算 -- 執行大量的類似工作以得到期望的結果。 定期處理、轉換和分析大量資料的組織最常使用批次運算。

Batch 很適合處理本質平行 (也稱為「超簡單平行」) 的應用程式和工作負載。 本質上平行的工作負載可輕易分割成多個工作，以在多部電腦上同時執行。

![平行工作][1]<br/>

常見使用這項技術處理的一些工作負載範例如下：

* 財務風險模型
* 氣候和水文資料分析
* 影像轉譯、分析和處理
* 媒體編碼及轉碼
* 基因序列分析
* 工程壓力分析
* 軟體測試

Batch 也可以執行平行計算 (最後加上歸納步驟)，以及執行其他更複雜的 HPC 工作負載，例如 [訊息傳遞介面 (MPI)](batch-mpi.md) 應用程式。

如需 Batch 與 Azure 中其他 HPC 解決方案選項的比較，請參閱 [Batch 和 HPC 解決方案](batch-hpc-solutions.md)。

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="developing-with-batch"></a>使用 Batch 進行開發
使用 Azure Batch 處理平行工作負載通常會透過其中一個 [Batch API](#batch-development-apis)，以程式設計方式進行。 用戶端應用程式或服務可使用 Batch API 來與 Batch 服務進行通訊。 Batch API 可讓您建立和管理計算節點 (虛擬機器或雲端服務) 集區。 您可以接著排程要在這些節點上執行的作業及工作。 

您可以為組織有效率地處理大量工作負載，或提供前端服務給客戶，讓他們可以在一個、數百個或甚至數千個節點上，依需要或依排程執行作業和工作。 您也可以在 [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md)之類的工具所管理的大型工作流程中使用 Azure Batch。

> [!TIP]
> 當您準備鑽研 Batch API 以深入了解它所提供的功能時，請參閱 [適用於開發人員的 Batch 功能概觀](batch-api-basics.md)。
> 
> 

### <a name="azure-accounts-youll-need"></a>您需要的 Azure 帳戶
當您開發 Batch 解決方案時，您將在 Microsoft Azure 中使用下列帳戶。

* **Azure 帳戶和訂用帳戶** - 如果您沒有 Azure 訂用帳戶，您可以啟用 [MSDN 訂戶權益][msdn_benefits]，或是註冊[免費 Azure 帳戶][free_account]。 當您建立帳戶時，會為您建立預設訂用帳戶。
* **Batch 帳戶** - Azure Batch 資源 (包括集區、計算節點、作業和工作) 都與 Azure Batch 帳戶相關聯。 當您的應用程式對 Batch 服務提出要求時，可以使用 Azure Batch 帳戶名稱、帳戶的 URL 及存取金鑰驗證此要求。 您可以在 Azure 入口網站中 [建立 Batch 帳戶](batch-account-create-portal.md) 。
* **儲存體帳戶** - Batch 內建就支援處理 [Azure 儲存體][azure_storage]中的檔案。 幾乎每個 Batch 案例都會使用 Azure Blob 儲存體，來預備工作執行的程式及其處理的資料，以及儲存其產生的輸出資料。 若要建立儲存體帳戶，請參閱 [關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

### <a name="batch-development-apis"></a>Batch 開發 API
應用程式和服務可以發出直接的 REST API 呼叫，或使用一或多個下列用戶端程式庫來執行和管理 Azure Batch 工作負載。

| API | API 參考資料 | 下載 | 教學課程 | 程式碼範例 |
| --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |N/A |- |- |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[教學課程](batch-dotnet-get-started.md) |[GitHub][api_sample_net] |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[教學課程](batch-python-tutorial.md)|[GitHub][api_sample_python] |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- |
| **Batch Java** (預覽) |[github.io][api_java] |[Maven][api_java_jar] |- |[GitHub][api_sample_java] |

### <a name="batch-command-line-tools"></a>Batch 命令列工具

開發 API 所提供的功能也可透過命令列工具取得︰ 

* [Batch PowerShell Cmdlet][batch_ps]：[Azure PowerShell](/powershell/azureps-cmdlets-docs) 模組中的 Batch PowerShell Cmdlet 可讓您使用 PowerShell 來管理 Batch 資源。
* [Azure CLI](../xplat-cli-install.md)：Azure 命令列介面 (Azure CLI) 是跨平台工具組，可提供用來與許多 Azure 服務 (包括 Batch) 互動的殼層命令。

### <a name="batch-resource-management"></a>Batch 資源管理

適用於 Batch 的 Azure Resource Manager API 會提供 Batch 帳戶的程式設計存取方式。 使用這些 API，您可以透過程式設計方式管理 Batch 帳戶、配額和應用程式套件。  

| API | API 參考資料 | 下載 | 教學課程 | 程式碼範例 |
| --- | --- | --- | --- | --- |
| **Batch Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [教學課程](batch-management-dotnet.md) |[GitHub][api_sample_net] |


### <a name="batch-tools"></a>Batch 工具
雖然不一定要使用 Batch 建置解決方案，但以下是建置和偵錯 Batch 應用程式和服務時所要使用的一些重要工具。

* [Azure 入口網站][portal]︰您可以在 Azure 入口網站的 Batch 刀鋒視窗中建立、監視和刪除 Batch 集區、作業和工作。 您可以在執行作業時檢視上述和其他資源的狀態資訊，甚至從您集區中的計算節點下載檔案 (例如，在進行疑難排解時下載失敗的工作 `stderr.txt` )。 您也可以下載可用來登入計算節點的遠端桌面 (RDP) 檔案。
* [Azure Batch 總管][batch_explorer]︰Batch 總管可提供與 Azure 入口網站類似的 Batch 資源管理功能，但是在獨立的 Windows Presentation Foundation (WPF) 用戶端應用程式中。 在 [GitHub][github_samples] 上可取得其中一個 Batch .NET 範例應用程式，您可以使用 Visual Studio 2015 建置此種應用程式，並在開發及偵錯 Batch 解決方案時，使用它來瀏覽和管理 Batch 帳戶中的資源。 檢視作業、集區和工作詳細資訊、從計算節點下載檔案，以及使用您可透過 Batch 總管下載的遠端桌面 (RDP) 檔案從遠端連接到節點。
* [Microsoft Azure 儲存體總管][storage_explorer]：嚴格來說，雖然儲存體總管不算是 Azure Batch 工具，但卻是您開發和偵錯 Batch 解決方案時的另一個很實用的工具。

## <a name="scenario-scale-out-a-parallel-workload"></a>案例：相應放大平行工作負載
使用 Batch API 來與 Batch 服務互動的一個常見案例涉及在一組計算節點上相應放大本質平行工作，例如轉譯 3D 場景的影像。 例如，這組計算節點可能是您的「轉譯伺服器陣列」，提供數十、數百或甚至數千個核心來呈現作業。

下圖顯示一個常見的 Batch 工作流程，其中有一個用戶端應用程式或託管服務使用 Batch 執行平行工作負載。

![Batch 解決方案工作流程][2]

在這個常見的案例中，應用程式或服務執行下列步驟，在 Azure Batch 中處理計算工作負載：

1. 將**輸入檔**和處理這些檔案的**應用程式**上傳到您的 Azure 儲存體帳戶。 輸入檔可以是您的應用程式將會處理的任何資料，例如財務模型化資料或要轉碼的視訊檔案。 應用程式檔案可以是任何用於處理資料的應用程式，例如 3D 轉譯應用程式或媒體轉碼器。
2. 在 Batch 帳戶中建立計算節點的 Batch **集區** -- 這些節點是將執行工作的虛擬機器。 您需要指定屬性，例如[節點大小](../cloud-services/cloud-services-sizes-specs.md)、其作業系統，以及節點加入集區時要安裝的應用程式在 Azure 儲存體中的位置 (您在步驟 #1 中上傳的應用程式)。 您也可以設定集區來隨著工作所產生的工作負載而[自動調整](batch-automatic-scaling.md)。 自動調整功能可動態調整集區中的計算節點數目。
3. 建立 Batch **作業** 以在計算節點集區上執行工作負載。 當您建立作業時，您需要將它與 Batch 集區建立關聯。
4. 將 **工作** 加入至作業。 當您將工作加入至作業時，Batch 服務會自動排程工作在集區中的計算節點上執行。 每一項工作會使用您上傳的應用程式來處理輸入檔。
   
   * 4a. 工作執行之前，它可以將它要處理的資料 (輸入檔) 下載至它被指派的計算節點。 如果應用程式未安裝在節點上 (請參閱步驟 2#)，您可以從這裡下載它。 下載完成時，工作就會在它被指派的節點上執行。
5. 工作執行時，您可以查詢 Batch 來監視作業及其工作的進度。 用戶端應用程式或服務可透過 HTTPS 來與 Batch 服務進行通訊。 因為您可能會監視在數千個計算節點上執行的數千個工作，請務必[有效率地查詢 Batch 服務](batch-efficient-list-queries.md)。
6. 當工作完成時，它們可以將其輸出資料上傳至 Azure 儲存體。 您也可以直接從計算節點上的檔案系統擷取檔案。
7. 當您的監視偵測到作業中的工作已完成時，用戶端應用程式或服務可以下載輸出資料來進一步處理或評估。

請記住，這只是使用 Batch 的一種方式，此案例只描述它可用的幾項功能。 例如，您可以在每個計算節點上[以平行方式執行多項工作](batch-parallel-node-tasks.md)，也可以使用[作業準備和完成工作](batch-job-prep-release.md)來準備作業的節點，然後再清除。

## <a name="next-steps"></a>後續步驟
既然您已大致了解 Batch 服務，現在可以更深入探索服務，了解如何使用它來處理計算密集平行工作負載。

* 請參閱 [適用於開發人員的 Batch 功能概觀](batch-api-basics.md)，這是任何準備使用 Batch 的人員不可或缺的資訊。 本文包含 Batch 服務資源 (例如集區、節點、作業和工作) 的詳細資訊，以及在建置 Batch 應用程式時可使用的許多 API 功能。
* [開始使用適用於 .NET 的 Azure Batch 程式庫](batch-dotnet-get-started.md) ，了解如何使用 C# 和 Batch .NET 程式庫，透過一般的批次工作流程來執行簡單的工作負載。 本文應該是您學習如何使用 Batch 服務的第一站。 另外還有 [Python 版本](batch-python-tutorial.md) 的教學課程。
* 下載 [GitHub 上的程式碼範例][github_samples]，看看 C# 和 Python 如何與 Batch 相互作用，以排程和處理範例工作負載。
* 查看 [Batch 學習路徑][learning_path]，了解您在學習使用 Batch 時可用的資源。

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/en-us/rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png



<!--HONumber=Dec16_HO3-->


