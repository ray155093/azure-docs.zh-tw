---
title: "調整「適用於 MySQL 的 Azure 資料庫」伺服器的 Azuer CLI 範例 | Microsoft Docs"
description: "此範例 CLI 指令碼會在查詢計量之後，將「適用於 MySQL 的 Azure 資料庫」伺服器調整為不同的效能等級。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c54262218e6174dc85905cb067c334cc6a401946
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 監視和調整適用於 MySQL 的 Azure 資料庫伺服器
此範例 CLI 指令碼會在查詢計量之後，將單一「適用於 MySQL 的 Azure 資料庫」伺服器調整為不同的效能等級。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresource/providers/Microsoft.DBforMySQL/servers/mysqlserver4demo" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresource/providers/Microsoft.DBforMySQL/servers/mysqlserver4demo" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az mysql server update \
--resource-group myresource \
--name mysqlserver4demo \
--compute-units 100
```

## <a name="clean-up-deployment"></a>清除部署
在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。
```azurecli
az group delete --name myresource
```

## <a name="next-steps"></a>後續步驟
[「適用於 MySQL 的 Azure 資料庫」的 Azuer CLI 範例](../sample-scripts-azure-cli.md)

