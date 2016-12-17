---
title: "還原 Azure SQL 資料倉儲 (REST API) | Microsoft Docs"
description: "還原 Azure SQL 資料倉儲的 REST API 工作。"
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed4442c5bd7a955e8d380bcb84cd62cd07eca05


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>還原 Azure SQL 資料倉儲 (REST API)
> [!div class="op_single_selector"]
> * [概觀][概觀]
> * [入口網站][入口網站]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

在本文中，您將了解如何使用 REST API 來還原 Azure SQL 資料倉儲。

## <a name="before-you-begin"></a>開始之前
**請驗證您的 DTU 容量。**  每個 SQL 資料倉儲均由具有預設 DTU 配額的 SQL 伺服器裝載 (例如 myserver.database.windows.net)。  在您還原 SQL 資料倉儲之前，請確認您的 SQL 伺服器有足夠的剩餘 DTU 配額供要還原的資料庫使用。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱[要求 DTU 配額變更][要求 DTU 配額變更]。

## <a name="restore-an-active-or-paused-database"></a>還原作用中或已暫停的資料庫
還原資料庫：

1. 使用 Get 資料庫還原點作業取得資料庫還原點清單。
2. 使用[建立資料庫還原要求][建立資料庫還原要求]作業來開始還原。
3. 使用[資料庫作業狀態][資料庫作業狀態]作業來追蹤還原狀態。

> [!NOTE]
> 還原完成後，您可以遵循[在復原之後設定資料庫][在復原之後設定資料庫]來設定復原的資料庫。
> 
> 

## <a name="restore-a-deleted-database"></a>還原已刪除的資料庫
還原已刪除的資料庫：

1. 使用[列出可還原的已卸除資料庫][列出可還原的已卸除資料庫]作業來列出所有可還原的已刪除資料庫。
2. 使用[取得可還原的已卸除資料庫][取得可還原的已卸除資料庫]作業來取得您想要還原之已刪除資料庫的詳細資料。
3. 使用[建立資料庫還原要求][建立資料庫還原要求]作業來開始還原。
4. 使用[資料庫作業狀態][資料庫作業狀態]作業來追蹤還原狀態。

> [!NOTE]
> 若要在還原完成之後設定資料庫，請參閱[在復原之後設定資料庫][在復原之後設定資料庫]。
> 
> 

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][Azure SQL Database 商務持續性概觀]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database 商務持續性概觀]: ../sql-database/sql-database-business-continuity.md
[要求 DTU 配額變更]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[在復原之後設定資料庫]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[如何安裝和設定 Azure PowerShell]: ./powershell-install-configure.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[建立資料庫還原要求]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[資料庫作業狀態]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[取得可還原的已卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[列出可還原的已卸除資料庫]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[在 Azure App Service 中建立 Web 應用程式]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps



<!--HONumber=Nov16_HO3-->


