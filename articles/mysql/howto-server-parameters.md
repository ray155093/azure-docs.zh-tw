---
title: "如何在 MySQL 的 Azure 資料庫中設定伺服器參數 | Microsoft Docs"
description: "此文章說明如何使用 Azure 入口網站，在 MySQL 的 Azure 資料庫中設定可用伺服器參數。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/19/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 718bbf359253849fbc989c563ffd6d1099f92348
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>如何使用 Azure 入口網站在 MySQL 的 Azure 資料庫中設定伺服器參數

MySQL 的 Azure 資料庫支援某些伺服器參數的組態。 此文章說明如何使用 Azure 入口網站設定這些參數，並列出支援的參數、預設值和有效值範圍。 並非所有伺服器參數皆可調整。 只有列於此處的參數才受支援。

## <a name="navigate-to-server-parameters-blade-on-azure-portal"></a>瀏覽至 Azure 入口網站上的 [伺服器參數] 刀鋒視窗

登入 Azure 入口網站，然後按一下 MySQL 的 Azure 資料庫伺服器名稱。 在 [設定] 區段下，按一下 [伺服器參數] 以開啟 MySQL 的 Azure 資料庫之 [伺服器參數] 刀鋒視窗。

![Azure 入口網站伺服器參數刀鋒視窗](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>可設定的伺服器參數清單

下表列出目前支援的伺服器參數。 參數可根據您的應用程式需求加以設定。

> [!div class="mx-tableFixed"]
|參數名稱|預設值|範圍|描述|
|---|---|---|---|
|binlog_group_commit_sync_delay|1000|0, 11-1000000|控制二進位記錄認可等待多少毫秒之後，才將二進位記錄檔同步至磁碟。|
|binlog_group_commit_sync_no_delay_count|0|0-1000000|中止目前延遲之前所等待的最大交易數，如 binlog-group-commit-sync-delay 所指定。|
|character_set_server|LATIN1|BIG5、UTF8MB4 等。|使用 charset_name 作為預設伺服器字元集。|
|div_precision_increment|4|0-30|增加除法運算結果的小數位數。|
|group_concat_max_len|1024|4-16777216|GROUP_CONCAT() 以位元組為單位的允許結果長度上限。|
|innodb_adaptive_hash_index|開啟|開啟、關閉|啟用還是停用 innodb 彈性雜湊索引。|
|innodb_lock_wait_timeout|50|1-3600|InnoDB 交易等待資料列鎖定多少秒之後才放棄。|
|interactive_timeout|1800|10-1800|伺服器在互動式連線中等待活動多少秒之後才將它關閉。|
|log_bin_trust_function_creators|關|開啟、關閉|此變數適用於啟用二進位記錄時。 它可控制儲存的函數建立者是否可受信任，不建立將不安全的事件寫入二進位記錄的儲存函數。|
|log_queries_not_using_indexes|關|開啟、關閉|預期將所有資料列擷取至慢速查詢記錄的記錄查詢。|
|log_slow_admin_statements|關|開啟、關閉|在寫入至慢速查詢記錄的陳述式中包含慢速管理陳述式。|
|log_throttle_queries_not_using_indexes|0|0-4294967295|限制每分鐘可寫入至慢速查詢記錄的此類查詢數。|
|long_query_time|10|1-1E+100|如果查詢花費的時間高於此值數秒鐘，伺服器會將 Slow_queries 狀態變數遞增。|
|max_allowed_packet|536870912|1024-1073741824|mysql_stmt_send_long_data() C API 函數所傳送的一個封包或任何產生的/中繼字串，或任何參數的大小上限。|
|min_examined_row_limit|0|0-18446744073709551615|將大於所設定資料列數的查詢記錄到慢速查詢記錄中。 |
|server_id|3293747068|1000-4294967295|在複寫中用來為每個主要與從屬端提供唯一身分識別的伺服器識別碼。|
|slave_net_timeout|60|30-3600|從主要等待更多資料多少秒之後，從屬即會認定連線中斷、中止讀取，並嘗試重新連線。|
|slow_query_log|關|開啟、關閉|啟用或停用慢速查詢記錄。|
|sql_mode|已選取 0|ALLOW_INVALID_DATES、IGNORE_SPACE 等。|目前的伺服器 SQL 模式。|
|time_zone|系統|範例：-8:00、+05:30|伺服器時區。|
|wait_timeout|120|60-240|伺服器在非互動式連線中等待活動多少秒之後才將它關閉。|

## <a name="next-steps"></a>後續步驟
- [適用於 MySQL 的 Azure 資料庫的連線庫](concepts-connection-libraries.md)

