---
title: "DocumentDB 自動化 - Azure CLI 2.0 | Microsoft Docs"
description: "使用 Azure CLI 2.0 來管理 DocumentDB 資料庫帳戶。 DocumentDB 是適用於 JSON 資料的雲端式 NoSQL 資料庫。"
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b286a93d7cc5f962f969e877b2f487e56cbb1a95
ms.lasthandoff: 03/30/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-cli-20"></a>使用 Azure CLI 2.0 自動化 Azure DocumentDB 帳戶管理
> [!div class="op_single_selector"]
> * [Azure 入口網站](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

下列指南說明使用 Azure CLI 2.0 中可用的 DocumentDB 預覽命令自動化管理 DocumentDB 資料庫帳戶的命令。 它也包含在[多重區域資料庫帳戶][scaling-globally]中管理帳戶金鑰和容錯移轉優先順序的命令。 更新資料庫帳戶可讓您修改一致性原則和新增/移除區域。 如需跨平台管理 DocumentDB 資料庫帳戶，您可以使用 [Azure PowerShell](documentdb-manage-account-with-powershell.md)、[資源提供者 REST API][rp-rest-api] 或 [Azure 入口網站](documentdb-create-account.md)。

## <a name="getting-started"></a>開始使用

依照[如何安裝和設定 Azure CLI 2.0] [install-az-cli2] 中的指示，使用 Azure CLI 2.0 設定開發環境。

執行下列命令並遵循螢幕上的步驟來登入 Azure 帳戶。

    az login

如果您還沒有現有的[資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)，請建立一個：

    az group create --name <resourcegroupname> --location <resourcegrouplocation>
    az group list

`<resourcegrouplocation>` 必須是已正式推出 DocumentDB 的其中一個區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

### <a name="notes"></a>注意事項

* 執行 'az documentdb -h' 來取得可用命令的完整清單，或瀏覽[參考頁面][az-documentdb-ref]。
* 執行 'az documentdb <command> -h' 來取得每個命令之必要和選擇性參數的詳細資料清單。

## <a id="create-documentdb-account-cli"></a>建立 DocumentDB 資料庫帳戶

此命令可讓您建立 DocumentDB 資料庫帳戶。 將新的資料庫帳戶設定為單一區域或有特定[一致性原則](documentdb-consistency-levels.md)的[多重區域][scaling-globally]。 

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
                                    To enable portal access, include 104.42.195.92.
    --kind                        : The type of DocumentDB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

範例： 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>注意事項
* 位置必須是 DocumentDB 已正式運作的區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

## <a id="update-documentdb-account-cli"></a>更新 DocumentDB 資料庫帳戶

此命令可讓您更新 DocumentDB 資料庫帳戶屬性。 這包括一致性原則，以及資料庫帳戶存在的位置。

> [!NOTE]
> 此命令可讓您新增及移除區域，但不允許您修改容錯移轉優先順序。 若要修改容錯移轉優先順序，請參閱[下方](#modify-failover-priority-powershell)。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

範例： 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a>在 DocumentDB 資料庫帳戶中新增/移除區域

若要在現有 DocumentDB 資料庫帳戶中新增或移除區域，請使用 [update](#update-documentdb-account-cli) 命令並搭配 `--locations` 旗標。 下列範例示範如何建立新帳戶，並於隨後在帳戶中新增和移除區域。

範例：

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a>刪除 DocumentDB 資料庫帳戶

此命令可讓您刪除現有的 DocumentDB 資料庫帳戶。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

範例：

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a>取得 DocumentDB 資料庫帳戶的屬性

此命令可讓您取得現有 DocumentDB 資料庫帳戶的屬性。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

範例：

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a>列出帳戶金鑰

當您建立 DocumentDB 帳戶時，服務會產生兩個主要存取金鑰，用於存取 DocumentDB 帳戶時的驗證。 透過提供這兩個存取金鑰，DocumentDB 讓您可以重新產生金鑰，同時又不需中斷 DocumentDB 帳戶。 也提供驗證唯讀作業的唯讀金鑰。 有兩個讀寫金鑰 (主要和次要) 和兩個唯讀金鑰 (主要和次要)。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

範例：

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a>重新產生帳戶金鑰

您應定期變更 DocumentDB 帳戶的存取金鑰，讓連線更加安全。 指派的兩個存取金鑰可讓您在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持 DocumentDB 帳戶連線。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

範例：

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a>修改 DocumentDB 資料庫帳戶的容錯移轉優先順序

如果是多重區域資料庫帳戶，您可以變更 DocumentDB 資料庫帳戶所在之不同區域的容錯移轉優先順序。 如需有關 DocumentDB 資料庫帳戶中容錯移轉的詳細資訊，請參閱[使用 DocumentDB 全球發佈資料](documentdb-distribute-data-globally.md)。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

範例：

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>後續步驟
您已有了 DocumentDB 帳戶，下一步是建立 DocumentDB 資料庫。 您可以使用下列其中一個動作來建立資料庫：

* Azure 入口網站，如[使用 Azure 入口網站建立 DocumentDB 集合和資料庫](documentdb-create-collection.md)所述。
* GitHub 上 [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 儲存機制之 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 專案中的 C# .NET 範例。
* [DocumentDB SDK](documentdb-sdk-dotnet.md)。 DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。

建立您的資料庫之後，您必須在資料庫中[新增一或多個集合](documentdb-create-collection.md)，然後在集合中[新增文件](documentdb-view-json-document-explorer.md)。


在集合中有了文件之後，您便可以藉由使用入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或其中一個 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)，針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#ExecutingSqlQueries)。

若要深入了解 DocumentDB，請探索以下資源：

* [DocumentDB 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [DocumentDB 資源模型和概念](documentdb-resources.md)


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/en-us/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/

