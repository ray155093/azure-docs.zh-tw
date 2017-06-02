---
title: "Azure PowerShell 指令碼 - 還原 SQL Database | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 使用 PowerShell 還原 SQL Database"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 05/23/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 751c1ff5c8cd76885b91c16d2372d1cd9b0c4c39
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017

---

# <a name="restore-a-sql-database-using-powershell"></a>使用 PowerShell 還原 SQL Database

此 PowerShell 指令碼範例會從異地備援備份還原 Azure SQL Database，並將已刪除的資料庫還原為最新的備份。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "建立 SQL Database")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 | [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | 建立主機資料庫或彈性集區的邏輯伺服器。 | 
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | 在邏輯伺服器中將資料庫建立為單一或集區的資料庫。 |
[Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) | 取得資料庫的異地備援備份。 |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) | 還原 SQL Database。 |
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase) | 移除 Azure SQL Database。 |
| [Get-AzureRmSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | 取得可還原的已刪除資料庫。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。

