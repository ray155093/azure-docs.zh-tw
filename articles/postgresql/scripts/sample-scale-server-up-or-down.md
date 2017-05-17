---
title: "Azure CLI 指令碼 - 調整適用於 PostgreSQL 的 Azure 資料庫 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 在查詢計量之後，將「適用於 PostgreSQL 的 Azure 資料庫」伺服器調整為不同的效能等級。"
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 04/30/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 36134ca1a899c25ab896577815ce9108cae0d563
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>使用 Azure CLI 監視和調整單一 PostgreSQL 伺服器
此範例 CLI 指令碼會在查詢計量之後，將單一「適用於 PostgreSQL 的 Azure 資料庫」伺服器調整為不同的效能等級。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼
```azurecli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a PostgreSQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az postgres server create \
--name mypgserver-20170401 \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az postgres server update \
--resource-group myresourcegroup \
--name mypgserver-20170401 \
--compute-units 100
```

## <a name="clean-up-deployment"></a>清除部署
在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>後續步驟
- 如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。
- 您可以在[適用於 PostgreSQL 的 Azure 資料庫文件](../sample-scripts-azure-cli.md)中，找到其他「適用於 PostgreSQL 的 Azure 資料庫」CLI 指令碼範例。
- 如需有關調整的詳細資訊，請參閱[服務層](../concepts-service-tiers.md)及[計算單位和儲存體單位](../concepts-compute-unit-and-storage.md)。
