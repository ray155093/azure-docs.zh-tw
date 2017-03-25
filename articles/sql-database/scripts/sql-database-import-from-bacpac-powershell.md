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
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 6ca1560d6d3fa313bd24cb6226cf1e78992af257
ms.lasthandoff: 03/10/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>使用 PowerShell 從 bacpac 匯入到 SQL Database

此範例 PowerShell 指令碼會從 bacpac 匯入資料庫。  

在執行這個指令碼之前，請確定您已使用 `Add-AzureRmAccount` Cmdlet 建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!code-powershell[主要](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1 "建立 SQL Database")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [New-AzureRmResourceGroup]() | 建立用來存放所有資源的資源群組。 |
| [New-AzureRmSqlServer]() | 建立主控 SQL Database 的邏輯伺服器。 |
| [New-AzureRmSqlServerFirewallRule]() | 建立防火牆規則以允許從輸入的 IP 位址範圍存取伺服器上的所有 SQL Database。 |
| [New-AzureRmSqlDatabase]() | 在邏輯伺服器中建立 SQL Database。 |
| [Remove-AzureRmResourceGroup]() | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。
