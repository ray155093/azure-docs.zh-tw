---
title: "資料移轉的監視及疑難排解 (Stretch Database) | Microsoft Docs"
description: "了解如何監視資料移轉的狀態。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 2341d446-9909-4694-8bb8-d288582daf54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4efcb1b30f002d7baecaa46f9994dfbc9ad42dee


---
# <a name="monitor-and-troubleshoot-data-migration-stretch-database"></a>資料移轉的監視及疑難排解 (Stretch Database)
若要在 Stretch Database 監視器中監視資料移轉，請在 SQL Server Management Studio 中針對資料庫選取 [工作 | Stretch | 監視]  。

## <a name="check-the-status-of-data-migration-in-the-stretch-database-monitor"></a>在 Stretch Database 監視器中檢查資料移轉狀態
在 SQL Server Management Studio 中針對資料庫選取 [工作 | Stretch |監視]  ，即可開啟 Stretch Database 監視器並監視資料移轉。

* 監視器上半部會顯示啟用 Stretch 的 SQL Server Database 和遠端 Azure 資料庫一般資訊。
* 監視器的下半部會顯示資料庫中每個啟用 Stretch 的資料表資料移轉狀態。

![Stretch Database 監視器][StretchMonitorImage1]

## <a name="a-namemigrationacheck-the-status-of-data-migration-in-a-dynamic-management-view"></a><a name="Migration"></a>以動態管理檢視檢查資料移轉的狀態
開啟 **sys.dm\_db\_rda\_migration\_status** 動態管理檢視，查看已移轉的批次和資料列數量。 如需詳細資訊，請參閱 [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx)。

## <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>資料移轉的疑難排解
**已啟用延展功能之資料表中的資料列未移轉到 Azure。問題出在哪裡？**

有幾個問題可能會影響移轉。 請檢查下列項目。

* 檢查 SQL Server 電腦的網路連線。
* 檢查 Azure 防火牆沒有阻擋您的 SQL Server 連線到遠端端點。
* 檢查動態管理檢視 **sys.dm\_db\_rda\_migration\_status** 以取得最新批次的狀態。 如果發生錯誤，請檢查批次的 error\_number、error\_state 和 error\_everity 值。
  
  * 如需有關檢視的詳細資訊，請參閱 [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx)。
  * 如需 SQL Server 錯誤訊息內容的詳細資訊，請參閱 [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx)。

**Azure 防火牆正在封鎖來自本機伺服器的連線。**

您可能必須在 Azure 伺服器的 Azure 防火牆設定中新增一個規則，讓 SQL Server 與遠端 Azure 伺服器進行通訊。

## <a name="see-also"></a>另請參閱
[Stretch Database 的管理和疑難排解](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png



<!--HONumber=Dec16_HO2-->


