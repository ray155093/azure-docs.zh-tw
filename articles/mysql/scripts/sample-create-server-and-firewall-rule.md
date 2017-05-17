---
title: "建立單一「適用於 MySQL 的 Azure 資料庫」伺服器並設定防火牆規則的 Azure CLI 範例 | Microsoft Docs"
description: "此範例 CLI 指令碼會建立「適用於 MySQL 的 Azure 資料庫」伺服器，並設定伺服器等級防火牆規則。"
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
ms.openlocfilehash: fa961f123dddc2e6243d1efbd1d72b994179eb79
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>使用 Azure CLI 建立 MySQL 伺服器並設定防火牆規則
此範例 CLI 指令碼會建立「適用於 MySQL 的 Azure 資料庫」伺服器，並設定伺服器等級防火牆規則。 成功執行指令碼後，即可從所有 Azure 服務和已設定的 IP 位址存取 MySQL 伺服器。

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

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresource \
--server mysqlserver4demo \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>清除部署
 執行指令碼範例之後，請使用下列命令移除資源群組及其相關聯的所有資源。
```azurecli
az group delete --name myresource
```
## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| **命令** | **注意事項** |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| az mysql server create | 建立主控資料庫的 MySQL 伺服器。 |
| az mysql server firewall create | 建立防火牆規則以允許從輸入的 IP 位址範圍存取伺服器及其之下的資料庫。 |
| [az group delete](https://docs.microsoft.com/en-us/cli/azure/resource#delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
[「適用於 MySQL 的 Azure 資料庫」的 Azuer CLI 範例](../sample-scripts-azure-cli.md)

