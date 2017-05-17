---
title: "使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則 | Microsoft Docs"
description: "描述如何使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則
伺服器等級防火牆規則可讓系統管理員從特定的 IP 位址或 IP 位址範圍，管理和存取適用於 PostgreSQL 的 Azure 資料庫伺服器。 透過方便的 Azure CLI 命令，您可以建立、更新、刪除、列出及顯示防火牆規則，以管理您的伺服器。 如需「適用於 PostgreSQL 的 Azure 資料庫」防火牆的概觀，請參閱[適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則](concepts-firewall-rules.md)

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [「適用於 PostgreSQL 的 Azure 資料庫」伺服器和資料庫](quickstart-create-server-database-azure-cli.md)
- 安裝 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 命令列公用程式

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>設定「適用於 PostgreSQL 的 Azure 資料庫」的防火牆規則
**az postgres server firewall-rule** 命令是從 Azure CLI 中用來建立、刪除、列出、顯示及更新防火牆規則。 此 

## <a name="login-to-azure-and-list-servers"></a>登入 Azure 和列出伺服器
安全地連線 Azure CLI 與 Azure 帳戶。 使用 **az login** 命令來執行這項操作。
1. 從命令列執行下列命令。
```azurecli
az login
```
此命令會輸出下一個步驟中要使用的程式碼。

2. 使用網頁瀏覽器開啟 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 頁面並輸入程式碼。

3. 出現提示時，使用您的 Azure 認證登入。

4. 您的登入獲得授權之後，主控台就會列印訂用帳戶清單。
複製所需訂用帳戶的識別碼，以設定目前的訂用帳戶供未來使用。
```azurecli
az account set --subscription {your subscription id}
```
5. 如果您不確定名稱，請列出您的訂用帳戶和資源群組的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。

```azurecli
az postgres server list --resource-group myresourcegroup
```
請注意清單中的名稱屬性，這將用來指定要使用的 PostgreSQL 伺服器。 如有需要，請確認該伺服器的詳細資料，使用名稱屬性確認名稱正確︰

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>列出防火牆規則 
使用伺服器名稱和資源群組名稱，列出伺服器上現有的伺服器防火牆規則。 請注意，伺服器名稱屬性是在 **--server** 參數中指定，而不是 **--name** 參數。
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
根據預設，輸出會以 JSON 格式列出規則 (若有的話)。 您可以使用參數 **--output table**，以更容易閱讀的資料表格式呈現輸出。
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>建立防火牆規則
使用適用於 PostgreSQL 的 Azure 資料庫伺服器名稱和資源群組名稱，在伺服器上建立新的防火牆規則。 提供規則的名稱、規則的起始 IP 和結束 IP，以涵蓋允許存取的 IP 位址範圍。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
若要允許存取單一 IP 位址，請在 [起始 IP] 和 [結束 IP] 中提供相同的位址，如這個範例所示。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
成功時，命令輸出會列出您已建立之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息文字。

## <a name="update-firewall-rule"></a>更新防火牆規則 
使用適用於 PostgreSQL 的 Azure 資料庫伺服器名稱和資源群組名稱，在伺服器上更新現有的防火牆規則。 提供現有防火牆規則的名稱作為輸入，以及要更新的起始 IP 和結束 IP 屬性。
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
成功時，命令輸出會列出您已更新之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息文字。
> [!NOTE]
> 如果防火牆規則不存在，更新命令會建立它。

## <a name="show-firewall-rule-details"></a>顯示防火牆規則詳細資料
使用適用於 PostgreSQL 的 Azure 資料庫伺服器名稱和資源群組名稱，顯示伺服器上現有的防火牆規則詳細資料。 提供現有防火牆規則的名稱作為輸入。
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
成功時，命令輸出會列出您已指定之防火牆規則的詳細資料，預設為 JSON 格式。 如果失敗，輸出就會改為顯示錯誤訊息文字。

## <a name="delete-firewall-rule"></a>刪除防火牆規則
使用適用於 PostgreSQL 的 Azure 資料庫伺服器名稱和資源群組名稱，從伺服器移除現有的防火牆規則。 提供現有防火牆規則的名稱。
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
成功時，沒有任何輸出。 發生錯誤時，就會傳回錯誤訊息文字。

## <a name="next-steps"></a>後續步驟
- 同樣地，您可以透過網頁瀏覽器，[使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-portal.md)
- 進一步了解[適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則](concepts-firewall-rules.md)
- 如需連線至「適用於 PostgreSQL 的 Azure 資料庫」伺服器的說明，請參閱[「適用於 PostgreSQL 的 Azure 資料庫」的連線庫](concepts-connection-libraries.md)

