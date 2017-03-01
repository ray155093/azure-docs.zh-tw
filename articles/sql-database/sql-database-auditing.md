---
title: "Azure SQL Database - 稽核 | Microsoft Docs"
description: "Azure SQL Database 稽核會追蹤資料庫事件並將事件寫入您 Azure 儲存體帳戶中的稽核記錄。"
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
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>SQL 資料庫稽核概念
Azure SQL Database 稽核會追蹤資料庫事件並將事件寫入您 Azure 儲存體帳戶中的稽核記錄。

稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。

稽核會啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。

* [Azure SQL Database 稽核概觀]
* [設定資料庫的稽核]
* [分析稽核記錄和報告]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Azure SQL Database 稽核概觀
SQL Database 稽核可讓您：

* **保留** 所選事件的稽核記錄。 您可以定義要稽核的資料庫動作類別。
* **報告** 資料庫活動。 您可以使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
* **分析** 報告。 您可以尋找可疑事件、異常活動及趨勢。

「稽核方法」有兩種：

* **Blob 稽核** - 記錄會寫入 Azure Blob 儲存體。 這是一個較新的稽核方法，可提供更高效能、支援更高資料粒度物件層級稽核，以及更符合成本效益。
* **資料表稽核** - 記錄會寫入 Azure 資料表儲存體。

> [!IMPORTANT]
> 引進新的「Blob 稽核」為資料庫繼承伺服器稽核原則的方式帶來重大的變更。 

您可以針對不同類型的事件類別設定稽核。

* 若要使用 Azure 入口網站來設定和管理稽核，請參閱[使用 Azure 入口網站稽核](sql-database-auditing-portal.md)。
* 若要使用 PowerShell 設定和管理稽核，請參閱[使用 PowerShell 稽核](sql-database-auditing-powershell.md)。
* 若要使用 REST API 來設定和管理稽核，請參閱[使用 REST API 稽核](sql-database-auditing-rest.md)。

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

可以針對特定資料庫定義稽核原則，或將稽核原則定義為預設伺服器原則。 預設伺服器稽核原則會套用至伺服器上所有現有和新建立的資料庫。

## <a name="blob-auditing"></a>Blob 稽核

如果伺服器 Blob 稽核已啟用，它一律會套用到資料庫 (將會稽核伺服器上的所有資料庫)，不論：
    - 資料庫稽核設定為何。
    - 是否已選取資料庫刀鋒視窗中的 [從伺服器繼承設定] 核取方塊。

> [!IMPORTANT]
> 如果在伺服器和資料庫上都啟用 Blob 稽核，這將「不會」覆寫或變更伺服器 Blob 稽核的任何設定，這兩種稽核會同時存在。 換句話說，系統將會對資料庫進行兩次相同的稽核 (一次是由伺服器原則，一次是由資料庫原則)。

您應該避免同時啟用伺服器 Blob 稽核與資料庫 Blob 稽核，除非：
 * 您需要為特定資料庫使用不同的*儲存體帳戶*或*保留期間*。
 * 您想要對此伺服器上某個特定資料庫，執行與其餘資料庫不同的事件類型或類別的稽核 (例如，只需對特定資料庫的資料表插入進行稽核)。

否則，建議只啟用伺服器層級 Blob 稽核，並讓所有資料庫的資料庫層級稽核保留在停用狀態。

## <a name="table-auditing"></a>資料表稽核

如果伺服器層級資料表稽核已啟用，則只在資料庫刀鋒視窗中 [從伺服器繼承設定] 核取方塊已選取的情況下 (針對所有現有和新建立的資料庫，預設都會選取此選項)，它才會套用到資料庫。

- 如果該核取方塊*已選取*，對資料庫稽核設定所作的任何變更都會**覆寫**此資料庫的伺服器設定。

- 如果該核取方塊*未選取*且資料庫層級稽核已停用，將不會稽核資料庫。

## <a name="analyze-audit-logs-and-reports"></a>分析稽核記錄和報告
稽核記錄會在您於安裝期間選擇的 Azure 儲存體帳戶中彙總。

您可以使用工具 (例如 [Azure 儲存體總管](http://storageexplorer.com/)) 來探索稽核記錄。

## <a name="next-steps"></a>後續步驟

* 若要使用 Azure 入口網站來設定和管理稽核，請參閱[在 Azure 入口網站中設定稽核](sql-database-auditing-portal.md)。
* 若要使用 PowerShell 設定和管理稽核，請參閱[使用 PowerShell 設定稽核](sql-database-auditing-powershell.md)。
* 若要使用 REST API 設定和管理稽核，請參閱[使用 REST API 設定稽核](sql-database-auditing-rest.md)。


<!--Anchors-->
[Azure SQL Database 稽核概觀]: #subheading-1
[設定資料庫的稽核]: #subheading-2
[分析稽核記錄和報告]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

