---
title: "備份已啟用延展功能的資料庫 |Microsoft Docs"
description: "了解如何備份已啟用 Stretch 的資料庫。\\-"
services: sql-server-stretch-database
documentationcenter: 
author: Antvgski
manager: johnmac
editor: douglasl
ms.assetid: a196f858-ef8f-47b5-b9db-bb7db98d48bd
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 68aeee33dbbba67e2a24300db62bf6bcdc627ea5


---
# <a name="backup-stretch-enabled-databases"></a>備份已啟用延展功能的資料庫
資料庫備份可協助您從眾多類型的失敗、錯誤及災害進行復原。  

* 您必須備份已啟用延展功能的 SQL Server 資料庫。  
* Microsoft Azure 會自動備份 Stretch Database 已從 SQL Server 移轉到 Azure 的遠端資料。  

> [!NOTE]
> 備份只是完整的高可用性及商務持續性解決方案的一部分。 如需有關高可用性的詳細資訊，請參閱 [高可用性解決方案](https://msdn.microsoft.com/library/ms190202.aspx)。
> 
> 

## <a name="back-up-your-sql-server-data"></a>備份您的 SQL Server 資料
若要備份已啟用延展功能的 SQL Server 資料庫，您可以繼續使用您目前使用的 SQL Server 備份方法。 如需詳細資訊，請參閱 [SQL Server 資料庫的備份與還原](https://msdn.microsoft.com/library/ms187048.aspx)。

已啟用延展功能之 SQL Server 資料庫的備份只包含執行備份時，該時間點的本機資料及符合移轉資格的資料。 \(合格資料是尚未移轉但將根據資料表的移轉設定移轉到 Azure 的資料。\) 這稱為「淺層」備份，其中不包括已經移轉到 Azure 的資料。  

## <a name="back-up-your-remote-azure-data"></a>備份您的遠端 Azure 資料
Microsoft Azure 會自動備份 Stretch Database 已從 SQL Server 移轉到 Azure 的遠端資料。  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure 藉由自動備份降低資料遺失風險
Azure 上的 SQL Server Stretch Database 服務會至少每 8 小時自動建立一次儲存體快照，來保護您的遠端資料庫。 每個快照會保留 7 天，為您提供一系列可能的還原點。  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure 藉由異地備援降低資料遺失風險
Azure 資料庫備份儲存在異地備援「Azure 儲存體」(RA-GRS) 上，因此預設為異地備援資料庫。 異地備援儲存體會將資料複寫到與主要區域距離數百英哩的次要區域。 在主要和次要區域中，您的資料都會複寫三次，且跨越不同的容錯網域和升級網域。 這可確保在即使發生全區中斷或災害而造成其中一個 Azure 區域無法供使用的情況下，仍可持續提供資料。

### <a name="a-namestretchrpoastretch-database-reduces-the-risk-of-data-loss-for-your-azure-data-by-retaining-migrated-rows-temporarily"></a><a name="stretchRPO"></a>Stretch Database 透過暫時保留已移轉的資料，降低 Azure 資料遺失風險
在 Stretch Database 將合格資料列從 SQL Server 移轉到 Azure 之後，它會將這些資料列保留在暫存資料表中至少 8 小時。 如果您還原 Azure 資料庫的備份，Stretch Database 將會使用暫存資料表中儲存的資料列，將 SQL Server 與 Azure 資料庫重新同步化。

還原 Azure 資料的備份之後，您必須執行 [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) 預存程序將已啟用延展功能的 SQL Server 資料庫重新連接到遠端 Azure 資料庫。 當您執行 **sys.sp_rda_reauthorize_db** 時，Stretch Database 會自動將 SQL Server 與 Azure 資料庫重新同步化。

若要增加 Stretch Database 在暫存資料表中暫時保留已移轉之資料的時數，請執行 [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) 預存程序並指定大於 8 的時數。 若要決定所要保留的資料多寡，請考量下列因素︰

* 自動 Azure 備份的頻率 (至少每 8 小時一次)。
* 問題發生後辨識問題及決定還原備份所需的時間。
* Azure 還原作業的持續時間。

> [!NOTE]
> 增加 Stretch Database 在暫存資料表中暫時保留的資料數量會增加 SQL Server 上所需的空間大小。
> 
> 

若要檢查 Stretch Database 目前在暫存資料表中暫時保留資料的時數，請執行 [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx) 預存程序。

## <a name="see-also"></a>另請參閱
[Manage and troubleshoot Stretch Database (Stretch Database 的管理和疑難排解)](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[備份和還原 SQL Server 資料庫](https://msdn.microsoft.com/library/ms187048.aspx)




<!--HONumber=Nov16_HO3-->


