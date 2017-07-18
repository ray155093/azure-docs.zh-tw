---
title: "如何在適用於 PostgreSQL 的 Azure 資料庫中還原伺服器 | Microsoft Docs"
description: "本文說明如何使用 Azure CLI 命令列，在適用於 PostgreSQL 的 Azure 資料庫中備份及還原伺服器。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fa63ccedb455e4972bfdd0e15069ad5e932b0fa3
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>如何使用 Azure CLI，在適用於 PostgreSQL 的 Azure 資料庫中備份和還原伺服器

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [「適用於 PostgreSQL 的 Azure 資料庫」伺服器和資料庫](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="backup-happens-automatically"></a>備份會自動進行
使用適用於 PostgreSQL 的 Azure 資料庫時，資料庫服務每隔 5 分鐘會自動備份一次服務。 

使用基本層時，備份會保留 7 天，而使用標準層時會保留 35 天。 如需詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫定價層](concepts-service-tiers.md)

透過這個自動備份功能，您可以將伺服器和其所有資料庫還原至新的伺服器，而且還原至更早的時間點。

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-cli"></a>使用 Azure CLI 將資料庫還原到先前的時間點
適用於 PostgreSQL 的 Azure 資料庫可讓您將伺服器還原至過去的時間點。 還原的資料會複製到新的伺服器，而現有的伺服器則保持原狀。 例如，如果資料表在今天中午意外卸除，您可以還原到中午之前的時間。 然後從伺服器的還原複本擷取遺失的資料表和資料。 

使用 [az postgres server restore](/cli/azure/postgres/server#restore) Azure CLI 命令來執行還原。

### <a name="run-the-restore-command"></a>執行 restore 命令
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

`az postgres server restore` 命令需要下列參數：
| 設定 | 建議的值 | 說明  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  來源伺服器所在的資源群組。  |
| 名稱 | mypgserver-restored | 還原命令所建立之新伺服器的名稱。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 選取所要還原的時間點。 這個日期和時間必須在來源伺服器的備份保留期限內。 請使用 ISO8601 日期和時間格式。 例如，您可能會使用您自己的本地時區，例如 `2017-04-13T05:59:00-08:00`，或使用 UTC Zulu 格式 `2017-04-13T13:59:00Z`。 |
| source-server | mypgserver-20170401 | 要進行還原的來源伺服器之名稱或識別碼。 |

將伺服器還原至時間點可建立新的伺服器，自指定的時間點起複製作為原始伺服器。 還原伺服器的位置與定價層值與來源伺服器相同。 命令是同步的，並在等待伺服器還原之後將會傳回。 

一旦還原完成時，找出所建立的新伺服器。 請確認資料已如預期般還原。

## <a name="next-steps"></a>後續步驟
[「適用於 PostgreSQL 的 Azure 資料庫」的連線庫](concepts-connection-libraries.md)

