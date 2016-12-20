---
title: "暫停和繼續資料移轉 (Stretch Database) | Microsoft Docs"
description: "了解如何暫停或繼續資料移轉至 Azure。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>暫停和繼續資料移轉 (Stretch Database)
若要暫停或繼續將資料移轉至 Azure，請在 SQL Server Management Studio 中針對資料表選取 [Stretch]，然後選取 [暫停] 來暫停資料移轉，或選擇 [繼續] 來繼續資料移轉。 您也可以使用 Transact-SQL 來暫停或繼續資料移轉。

若您想在本機伺服器上針對問題疑難排解，或想將可用的網路頻寬最大化時，可在個別資料表上暫停資料移轉。

## <a name="pause-data-migration"></a>暫停資料移轉
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>使用 SQL Server Management Studio 暫停資料移轉
1. 在 SQL Server Management Studio 中，請選取 [物件總管] 中想要暫停資料移轉的 Stretch 資料表。
2. 以滑鼠右鍵按一下並選取 [Stretch]，然後選取 [暫停]。

### <a name="use-transact-sql-to-pause-data-migration"></a>使用 Transact\-SQL 暫停資料移轉
執行下列命令。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>繼續資料移轉
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>使用 SQL Server Management Studio 繼續資料移轉
1. 在 SQL Server Management Studio 中，請選取 [物件總管] 中想要繼續資料移轉的 Stretch 資料表。
2. 以滑鼠右鍵按一下並選取 [Stretch]，然後選取 [繼續]。

### <a name="use-transact-sql-to-resume-data-migration"></a>使用 Transact\-QL 繼續資料移轉
執行下列命令。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>查看移轉是否為作用中或已暫停
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>使用 SQL Server Management Studio 查看移轉是否為作用中或已暫停
在 SQL Server Management Studio 中，開啟 [Stretch Database 監視器]，然後查看 [移轉狀態]欄中的值。 如需詳細資訊，請參閱 [資料移轉的監視及疑難排解](sql-server-stretch-database-monitor.md)。

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>使用 Transact-SQL 查看移轉是否為作用中或已暫停
查詢 **sys.remote_data_archive_tables** 目錄檢視，並查看 **is_migration_paused** 欄的值。 如需詳細資訊，請參閱 [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx)。

## <a name="see-also"></a>另請參閱
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[資料移轉的監視及疑難排解](sql-server-stretch-database-monitor.md)




<!--HONumber=Nov16_HO3-->


