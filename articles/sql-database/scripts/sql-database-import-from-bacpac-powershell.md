---
title: "Azure PowerShell 指令碼 - 匯入 - bacpac - SQL Database | Microsoft Docs"
description: "Azure PowerShell 指令碼範例 - 使用 PowerShell 從 bacpac 匯入到 SQL Database"
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
ms.openlocfilehash: cd6c53b7e4cc13920f1ed99934e10a38a9b3e4ed
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>使用 PowerShell 從 bacpac 匯入到 SQL Database

此範例 PowerShell 指令碼會從 **bacpac**檔案匯入資料庫。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1?highlight=18-19 "建立 SQL Database")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | 建立主控 SQL Database 的邏輯伺服器。 |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | 建立防火牆規則以允許從輸入的 IP 位址範圍存取伺服器上的所有 SQL Database。 |
| [New-AzureRmSqlDatabaseImport (英文)](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) | 在伺服器上匯入 .bacpac 檔案並建立新的資料庫。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。

