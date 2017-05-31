---
title: "Azure Cosmos DB 自動化 - Azure CLI 2.0 | Microsoft Docs"
description: "使用 Azure CLI 2.0 來建立及管理 Azure Cosmos DB 帳戶。 Azure Cosmos DB 是高可用性的全域散發資料庫。"
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.custom: quick start create
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6150914e623cb6c54f1257c772be62150ab81e17
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-cosmos-db-account-using-the-azure-cli"></a>使用 Azure CLI 建立 Azure Cosmos DB 帳戶

下列指南說明使用 Azure CLI 2.0 中可用的預覽命令，自動管理 Azure Cosmos DB 資料庫帳戶的命令。 它也包含在[多重區域資料庫帳戶][scaling-globally]中管理帳戶金鑰和容錯移轉優先順序的命令。 更新資料庫帳戶可讓您修改一致性原則和新增/移除區域。 如需跨平台管理 Azure Cosmos DB 資料庫帳戶，您可以使用 [Azure PowerShell](documentdb-manage-account-with-powershell.md)、[資源提供者 REST API][rp-rest-api] 或 [Azure 入口網站](documentdb-create-account.md)。

## <a name="getting-started"></a>開始使用

依照[如何安裝和設定 Azure CLI 2.0] [install-az-cli2] 中的指示，使用 Azure CLI 2.0 設定開發環境。

執行下列命令並遵循螢幕上的步驟來登入 Azure 帳戶。

```azurecli
az login
```

如果您還沒有現有的[資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)，請建立一個：

```azurecli
az group create --name <resourcegroupname> --location <resourcegrouplocation>
az group list
```

`<resourcegrouplocation>` 必須是已正式推出 Azure Cosmos DB 的其中一個區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

### <a name="notes"></a>注意事項

* 執行 'az documentdb -h' 來取得可用命令的完整清單，或瀏覽[參考頁面][az-documentdb-ref]。
* 執行 'az documentdb &lt;command&gt; -h' 來取得每個命令之必要和選擇性參數的詳細資料清單。

## <a name="register-your-subscription-to-use-azure-cosmos-db"></a>使用 Azure Cosmos DB 註冊您的訂用帳戶

此命令會透過 CLI 註冊您的訂用帳戶來使用 Azure Cosmos DB。

```azurecli
az provider register -n Microsoft.DocumentDB 
```

## <a id="create-documentdb-account-cli"></a> 建立 Azure Cosmos DB 資料庫帳戶

此命令可讓您建立 Azure Cosmos DB 資料庫帳戶。 將新的資料庫帳戶設定為單一區域或有特定[一致性原則](documentdb-consistency-levels.md)的[多重區域][scaling-globally]。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account. The account 
                                    name must be unique.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of Azure Cosmos DB database account to create.  Allowed
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

```azurecli
az documentdb create -g <resourcegroupname> -n <uniquedocumentdbaccountname> --kind <typeofdatabaseaccount>
```

範例： 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>注意事項 
* locations 必須是已正式推出 Azure Cosmos DB 的區域。 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。
* 若要啟用入口網站存取權，請將您所在區域的 Azure 入口網站 IP 位址包括在 ip-range-filter 中，如[設定 IP 存取控制原則](documentdb-firewall-support.md#configure-ip-policy)中所指定。

## <a id="update-documentdb-account-cli"></a> 更新 Azure Cosmos DB 資料庫帳戶

此命令可讓您更新 Azure Cosmos DB 資料庫帳戶屬性。 這包括一致性原則，以及資料庫帳戶存在的位置。

> [!NOTE]
> 此命令可讓您新增及移除區域，但不允許您修改容錯移轉優先順序。 若要修改容錯移轉優先順序，請參閱[下方](#modify-failover-priority-powershell)。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
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

## <a id="add-remove-region-documentdb-account-cli"></a> 從 Azure Cosmos DB 帳戶新增/移除區域

若要在現有 Azure Cosmos DB 資料庫帳戶中新增或移除區域，請使用 [update](#update-documentdb-account-cli) 命令並搭配 `--locations` 旗標。 下列範例示範如何建立新帳戶，並於隨後在帳戶中新增和移除區域。

範例：

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> 刪除 Azure Cosmos DB 資料庫帳戶

此命令可讓您刪除現有的 Azure Cosmos DB 資料庫帳戶。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

範例：

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> 取得 Azure Cosmos DB 資料庫帳戶的屬性

此命令可讓您取得現有 Azure Cosmos DB 資料庫帳戶的屬性。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

範例：

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a>列出帳戶金鑰

當您建立 Azure Cosmos DB 帳戶時，服務會產生兩個主要存取金鑰，用於存取 Azure Cosmos DB 帳戶時的驗證。 透過提供這兩個存取金鑰，Azure Cosmos DB 讓您可以重新產生金鑰，同時又不需中斷 Azure Cosmos DB 帳戶。 也提供驗證唯讀作業的唯讀金鑰。 有兩個讀寫金鑰 (主要和次要) 和兩個唯讀金鑰 (主要和次要)。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

範例：

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> 列出連接字串

對於 MongoDB 帳戶，您可以使用下列命令來擷取將您的 MongoDB 應用程式連線到資料庫帳戶的連接字串。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

範例：

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a>重新產生帳戶金鑰

您應定期變更 Azure Cosmos DB 帳戶的存取金鑰，讓連線更加安全。 指派的兩個存取金鑰可讓您在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持 Azure Cosmos DB 帳戶連線。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

範例：

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> 修改 Azure Cosmos DB 資料庫帳戶的容錯移轉優先順序

如果是多重區域資料庫帳戶，您可以變更 Azure Cosmos DB 資料庫帳戶所在之不同區域的容錯移轉優先順序。 如需有關 Azure Cosmos DB 資料庫帳戶中容錯移轉的詳細資訊，請參閱[使用 Azure Cosmos DB 全域散發資料](documentdb-distribute-data-globally.md)。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

範例：

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>後續步驟

* 若要使用 .NET 進行連線，請參閱[使用 .NET 進行連線和查詢](../cosmos-db/create-documentdb-dotnet.md)。
* 若要使用 .NET Core 進行連線，請參閱[使用 .NET Core 進行連線和查詢](../cosmos-db/create-documentdb-dotnet-core.md)。
* 若要使用 Node.js 進行連線，請參閱[使用 Node.js 和 MongoDB 應用程式進行連線和查詢](../cosmos-db/create-mongodb-nodejs.md)。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/

