---
title: "REST API：管理 Azure SQL Database 稽核 | Microsoft 文件"
description: "使用 REST API 設定 Azure SQL Database 稽核，以追蹤資料庫事件，然後將事件寫入您 Azure 儲存體帳戶中的稽核記錄。"
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
ms.openlocfilehash: 9516dd1ae6a67fb9402591fe1906ee7554c1495c
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-the-rest-api"></a>使用 REST API 設定和管理 SQL 資料庫稽核

本主題描述如何使用 REST API 設定和管理稽核。 若要使用 Azure 入口網站來設定和管理稽核，請參閱[在 Azure 入口網站中設定稽核](sql-database-auditing-portal.md)。 若要使用 PowerShell 設定和管理稽核，請參閱[使用 PowerShell 設定稽核](sql-database-auditing-powershell.md)。

如需稽核的概觀，請參閱 [SQL Database 稽核](sql-database-auditing.md)。

## <a name="rest-api---blob-auditing"></a>REST API - Blob 稽核

   * [建立或更新資料庫 Blob 稽核原則](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [建立或更新伺服器 Blob 稽核原則](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [取得資料庫 Blob 稽核原則](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [取得伺服器 Blob 稽核原則](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [取得伺服器 Blob 稽核操作結果](https://msdn.microsoft.com/library/azure/mt771862.aspx)


## <a name="rest-api---table-auditing"></a>REST API - 資料表稽核

   * [建立或更新資料庫稽核原則](https://msdn.microsoft.com/library/azure/mt604471.aspx)
   * [建立或更新伺服器稽核原則](https://msdn.microsoft.com/library/azure/mt604383.aspx)
   * [取得資料庫稽核原則](https://msdn.microsoft.com/library/azure/mt604381.aspx)
   * [取得伺服器稽核原則](https://msdn.microsoft.com/library/azure/mt604382.aspx)

## <a name="next-steps"></a>後續步驟

* 若要使用 Azure 入口網站來設定和管理稽核，請參閱[設定 Azure 入口網站中的資料庫稽核](sql-database-auditing-portal.md)。 
* 若要使用 PowerShell 設定和管理稽核，請參閱[使用 PowerShell 設定資料庫稽核](sql-database-auditing-powershell.md)。
* 如需稽核的概觀，請參閱[資料庫稽核](sql-database-auditing.md)。
