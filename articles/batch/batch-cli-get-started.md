---
title: "開始使用 Azure CLI for Batch | Microsoft Docs"
description: "取得 Azure CLI 中 Batch 命令的快速簡介，以便管理 Azure Batch 服務資源"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 01/23/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 698c481e2eff5e0a3b893a0377d9f4cd2f052eb4
ms.lasthandoff: 03/21/2017


---
# <a name="manage-batch-resources-with-azure-cli"></a>使用 Azure CLI 管理 Batch 資源

跨平台 Azure 命令列介面 (Azure CLI) 可讓您管理 Batch 帳戶和資源，例如 Linux、Mac 和 Windows 命令殼層中的集區、作業和工作。 許多使用 Batch API、Azure 入口網站和 Batch PowerShell Cmdlet 來執行的工作，也都可以使用 Azure CLI 來執行或撰寫指令碼。

本文章是根據 Azure CLI 0.10.5 版所撰寫。

## <a name="prerequisites"></a>必要條件
* [安裝 Azure CLI](../cli-install-nodejs.md)
* [將 Azure CLI 連接至您的 Azure 訂用帳戶](../xplat-cli-connect.md)
* 切換至 **Resource Manager 模式**：`azure config mode arm`

> [!TIP]
> 建議您經常更新您的 Azure CLI 安裝以利用服務更新和增強功能。
> 
> 

## <a name="command-help"></a>命令說明
您可以加上 `-h` 作為命令之後的唯一選項，以在 Azure CLI 中顯示每個命令的說明文字。 例如：

* 若要取得 `azure` 命令的說明，請輸入︰`azure -h`
* 若要取得 CLI 中所有 Batch 命令的清單，請使用︰`azure batch -h`
* 若要取得有關建立 Batch 帳戶的說明，請輸入︰ `azure batch account create -h`

如有疑問，請使用 `-h` 命令列選項來取得任何 Azure CLI 命令的說明。

## <a name="create-a-batch-account"></a>建立批次帳戶：
Usage :

    azure batch account create [options] <name>

範例：

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

使用指定的參數建立新的 Batch 帳戶。 您必須指定至少一個位置、資源群組和帳戶名稱。 如果您還沒有資源群組，請執行 `azure group create`建立，並針對 `--location`指定其中一個 Azure 區域 (例如「美國西部」)。 例如：

    azure group create --name "resgroup001" --location "West US"

> [!NOTE]
> Batch 帳戶名稱必須是建立帳戶的 Azure 區域內的唯一名稱。 它只能包含小寫英數字元，且長度必須為 3-24 個字元。 您不能在 Batch 帳戶名稱中使用特殊字元，例如 `-` 或 `_`。
> 
> 

### <a name="linked-storage-account-autostorage"></a>連結的儲存體帳戶 (autostorage)
您可以在建立 Batch 帳戶時，(選擇性地) 將 **一般用途** 的儲存體帳戶連結至該帳戶。 Batch 的 [應用程式套件](batch-application-packages.md)功能會在連結的一般用途儲存體帳戶中使用 blob 儲存體，如同 [Batch 檔案慣例 .NET](batch-task-output.md) 程式庫所為。 這些選擇性功能可協助您部署您的 Batch 工作所執行的應用程式，並保存其所產生的資料。

若要在建立新的 Batch 帳戶時，將現有的 Azure 儲存體帳戶連結到該帳戶，請指定 `--autostorage-account-id` 選項。 此選項需要儲存體帳戶的完整資源識別碼。

首先，顯示儲存體帳戶的詳細資料︰

    azure storage account show --resource-group "resgroup001" "storageaccount001"

然後使用 `--autostorage-account-id` 選項的 [Url] 值。 Url 值的開頭為 "/subscriptions/"，且包含您的訂用帳戶識別碼和儲存體帳戶的資源路徑︰

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>刪除 Batch 帳戶
Usage :

    azure batch account delete [options] <name>

範例：

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

刪除指定的 Batch 帳戶。 出現提示時，請確認您想要移除帳戶 (帳戶移除可能需要一些時間才能完成)。

## <a name="manage-account-access-keys"></a>管理帳戶存取金鑰
您需要存取金鑰才能在 Batch 帳戶中 [建立和修改資源](#create-and-modify-batch-resources) 。

### <a name="list-access-keys"></a>列出存取金鑰
Usage :

    azure batch account keys list [options] <name>

範例：

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

列出指定 Batch 帳戶的帳戶金鑰。

### <a name="generate-a-new-access-key"></a>產生新的存取金鑰
Usage :

    azure batch account keys renew [options] --<primary|secondary> <name>

範例：

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

為指定的 Batch 帳戶重新產生指定的帳戶金鑰。

## <a name="create-and-modify-batch-resources"></a>建立和修改批次資源
您可以使用 Azure CLI 來建立、讀取、更新和刪除 (CRUD) Batch 資源，例如集區、計算節點、作業和工作。 這些 CRUD 作業需要您的 Batch 帳戶名稱、存取金鑰和端點。 您可以使用 `-a`、`-k` 和 `-u` 選項指定這些項目，或設定 CLI 會自動使用的[環境變數](#credential-environment-variables) (若已填入)。

### <a name="credential-environment-variables"></a>認證環境變數
您可以設定 `AZURE_BATCH_ACCOUNT`、`AZURE_BATCH_ACCESS_KEY`和 `AZURE_BATCH_ENDPOINT` 環境變數，而不是在命令列上為您執行的每個命令指定 `-a`、`-k` 和 `-u` 選項。 Batch CLI 會使用這些變數 (若已設定)，所以您可省略 `-a`、`-k` 和 `-u` 選項。 本文的其餘部分假設使用這些環境變數。

> [!TIP]
> 使用 `azure batch account keys list`列出您的金鑰，並使用 `azure batch account show` 顯示帳戶的端點。
> 
> 

### <a name="json-files"></a>JSON 檔案
當您建立 Batch 資源 (如集區和工作) 時，您可以指定包含新資源組態的 JSON 檔案，而不是將它的參數當作命令列選項傳遞。 例如：

`azure batch pool create my_batch_pool.json`

雖然您可以使用命令列選項執行許多資源建立作業，但有些功能需要 JSON 格式的檔案 (內含資源詳細資料)。 例如，如果您想要指定啟動工作的資源檔，則必須使用 JSON 檔案。

若要尋找建立資源所需的 JSON，請參閱 MSDN 上的 [Batch REST API 參考][rest_api]文件。 每個「新增 &lt;資源類型&gt;」 主題都包含可供建立資源的範例 JSON，可用來作為 JSON 檔案的範本。 例如，在[將集區新增至帳戶][rest_add_pool]中可找到用於建立集區的 JSON。

> [!NOTE]
> 如果您在建立資源時指定 JSON 檔案，則會忽略您在命令列上為該資源指定的所有其他參數。
> 
> 

## <a name="create-a-pool"></a>建立集區
Usage :

    azure batch pool create [options] [json-file]

範例 (虛擬機器組態)︰

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

範例 (雲端服務組態)︰

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

在 Batch 服務中建立計算節點的集區。

如 [Batch 功能概關](batch-api-basics.md#pool)所提，針對集區中的節點選取作業系統時，您有兩個選項︰[虛擬機器組態] 和 [雲端服務組態]。 使用 `--image-*` 選項來建立虛擬機器組態集區，使用 `--os-family` 來建立雲端服務組態集區。 您無法同時指定 `--os-family` 和 `--image-*` 選項。

您可以指定集區[應用程式套件](batch-application-packages.md)以及[啟動工作](batch-api-basics.md#start-task)的命令列。 若要指定啟動工作的資源檔，您必須改用 [JSON 檔案](#json-files)。

刪除集區︰

    azure batch pool delete [pool-id]

> [!TIP]
> 檢查[虛擬機器映像清單](batch-linux-nodes.md#list-of-virtual-machine-images)中適合 `--image-*` 選項的值。
> 
> 

## <a name="create-a-job"></a>建立工作
Usage :

    azure batch job create [options] [json-file]

範例：

    azure batch job create --id "job001" --pool-id "pool001"

將作業新增至 Batch 帳戶，並指定其工作執行所在的集區。

刪除工作︰

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>列出集區、作業、工作及其他資源
每個 Batch 資源類型都支援 `list` 命令，已查詢 Batch 帳戶並列出該類型的資源。 例如，您可以列出您帳戶中的集區和作業中的工作︰

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>有效率地列出資源
為了加快查詢，您可以指定 `list` 作業的 **select**、**filter** 和 **expand** 子句選項。 使用這些選項來限制 Batch 服務所傳回的資料量。 因為所有篩選發生於伺服器端，只有您感興趣的資料會跨越網路。 當您執行作業清單時，使用這些子句來節省頻寬 (因而節省時間)。

例如，這只會傳回識別碼以 "renderTask" 開頭的集區︰

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

Batch CLI 支援 Batch 服務所支援的全部三個子句︰

* `--select-clause [select-clause]` 傳回每個實體的屬性子集
* `--filter-clause [filter-clause]` 傳回符合指定 OData 運算式的實體
* `--expand-clause [expand-clause]` 取得單一基礎 REST 呼叫中的實體資訊。 Expand 子句此時只支援 `stats` 屬性。

如需這三個子句以及執行清單查詢的詳細資訊，請參閱 [有效率地查詢 Azure Batch 服務](batch-efficient-list-queries.md)。

## <a name="application-package-management"></a>應用程式封裝管理
應用程式封裝提供了簡化的方式，可將應用程式部署至您集區中的計算節點。 您可以使用 Azure CLI，上傳應用程式封裝、管理封裝版本，以及刪除封裝。

若要建立新的應用程式並新增封裝版本︰

**建立** 應用程式：

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**新增** 應用程式封裝︰

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**啟動** 封裝︰

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

設定應用程式的**預設版本**︰

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>部署應用程式封裝
您可以在建立新集區時，指定一或多個應用程式封裝以供部署。 當您在建立集區時指定封裝時，它會在節點加入集區時部署到每個節點。 重新啟動或重新安裝映像節點時，也會部署套件。

在建立集區時指定 `--app-package-ref` 選項，以在節點加入集區時將應用程式套件部署到集區的節點。 `--app-package-ref` 選項接受一份要部署至計算節點的應用程式識別碼清單 (以分號分隔)。

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

當您使用命令列選項建立集區時，您目前無法指定「哪個」應用程式套件版本要部署至計算節點，例如 "1.10-beta3"。 因此，您必須在建立集區之前，先利用 `azure batch application set [options] --default-version <version-id>` 指定應用程式的預設版本 (請參閱上一節)。 不過，如果您在建立集區時使用 [JSON 檔案](#json-files)，而非使用命令列選項，即可為集區指定套件版本。

您可以在[使用 Azure Batch 應用程式套件部署應用程式](batch-application-packages.md)中，找到應用程式套件的詳細資訊。

> [!IMPORTANT]
> 您必須先 [連結 Azure 儲存體帳戶](#linked-storage-account-autostorage) 到您的 Batch 帳戶，才能使用應用程式套件。
> 
> 

### <a name="update-a-pools-application-packages"></a>更新集區的應用程式封裝
若要更新已指派給現有集區的應用程式，請發出搭配 `--app-package-ref` 選項的 `azure batch pool set` 命令︰

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

若要將新的應用程式套件部署至已在現有集區中的計算節點，您必須重新啟動這些節點或重新安裝其映像︰

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

> [!TIP]
> 您可以利用 `azure batch node list` 取得集區中的節點清單，以及其節點識別碼。
> 
> 

請記住，您必須在部署之前，已經設定應用程式的預設版本 (`azure batch application set [options] --default-version <version-id>`)。

## <a name="troubleshooting-tips"></a>疑難排解秘訣
本節旨在提供給您排解 Azure CLI 疑難問題時所要使用的資源。 這不一定能解決所有問題，但可協助您縮小原因並將您指向說明資源。

* 使用 `-h` 取得任何 CLI 命令的 **說明文字**
* 使用 `-v` 和 `-vv` 來顯示 **verbose`-vv` 命令輸出；** 是「額外的」詳細資訊，可顯示實際的 REST 要求和回應。 這些參數方便用於顯示完整的錯誤輸出。
* 您可以使用 `--json` 選項檢視 **JSON 格式的命令輸出**。 例如， `azure batch pool show "pool001" --json` 會以 JSON 格式顯示 pool001 的屬性。 您可以接著複製並修改此輸出，以便用於 `--json-file` (請參閱本文前面的 [JSON 檔案](#json-files) )。
* [MSDN 上的 Batch 論壇][batch_forum]是很棒的說明資源，並受到 Batch 團隊成員的密切監視。 如果您遇到問題或需要特定作業的協助，務必在此張貼您的問題。
* Azure CLI 目前並不支援每項 Batch 資源作業。 例如，您目前無法指定集區的應用程式封裝「版本」  ，只能指定封裝識別碼。 在這類情況下，您可能需要為您的命令提供 `--json-file` ，而不是使用命令列選項。 請務必隨時掌握最新的 CLI 版本，以便獲知未來的增強功能。

## <a name="next-steps"></a>後續步驟
* 請參閱 [使用 Azure Batch 應用程式封裝部署應用程式](batch-application-packages.md) ，了解如何使用此功能來管理和部署您在 Batch 計算節點上執行的應用程式。
* 如需減少項目數和針對 Batch 查詢所傳回之資訊類型的詳細資訊，請參閱 [有效率地查詢 Batch 服務](batch-efficient-list-queries.md) 。

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx

