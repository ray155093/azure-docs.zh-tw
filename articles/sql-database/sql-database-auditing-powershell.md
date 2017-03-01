---
title: "PowerShell：管理 Azure SQL Database 稽核 | Microsoft 文件"
description: "使用 PowerShell 設定 Azure SQL Database 稽核，以追蹤資料庫事件，然後將事件寫入您 Azure 儲存體帳戶中的稽核記錄。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>使用 PowerShell 設定和管理 SQL 資料庫稽核

下節描述如何使用 PowerShell 設定和管理稽核。 若要使用 Azure 入口網站來設定和管理稽核，請參閱[在 Azure 入口網站中設定稽核](sql-database-auditing-portal.md)。 若要使用 REST API 設定和管理稽核，請參閱[使用 REST API 設定稽核](sql-database-auditing-rest.md)。

如需稽核的概觀，請參閱 [SQL Database 稽核](sql-database-auditing.md)。

## <a name="powershell-cmdlets"></a>PowerShell Cmdlet

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>後續步驟

* 若要使用 Azure 入口網站來設定和管理稽核，請參閱[設定 Azure 入口網站中的資料庫稽核](sql-database-auditing-portal.md)。 
* 若要使用 PowerShell 設定和管理稽核，請參閱[使用 REST API 設定資料庫稽核](sql-database-auditing-rest.md)。
* 如需稽核的概觀，請參閱[資料庫稽核](sql-database-auditing.md)。


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

