---
title: "Azure 入口網站：管理 SQL Database 稽核 | Microsoft Docs"
description: "設定 Azure 入口網站中的 Azure SQL Database 稽核，以追蹤資料庫事件，然後將事件寫入您 Azure 儲存體帳戶中的稽核記錄。"
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
ms.date: 2/25/2017
ms.author: ronitr;giladm
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e20a30a565198c01f213895a87fe8807a37272cb
ms.lasthandoff: 03/30/2017


---

# <a name="configure-and-manage-sql-database-auditing-in-the-azure-portal"></a>設定和管理 Azure 入口網站中的 SQL 資料庫稽核

下節描述如何使用 Azure 入口網站設定和管理稽核。 若要使用 PowerShell 設定和管理稽核，請參閱[使用 PowerShell 設定稽核](scripts/sql-database-create-and-configure-database-powershell.md)。 

如需稽核的概觀，請參閱 [SQL Database 稽核](sql-database-auditing.md)。

## <a name="configure-blob-auditing"></a>設定 Blob 稽核

1. 啟動 [Azure 入口網站](https://portal.azure.com)，位址是 https://portal.azure.com。
2. 瀏覽至您想要稽核的 SQL Database / SQL Server 設定刀鋒視窗。 在 [設定] 刀鋒視窗中，選取 [稽核和威脅偵測]。

    ![瀏覽窗格](./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png)
3. 您可以在 [資料庫稽核組態] 刀鋒視窗中，選取 [從伺服器繼承稽核設定] 核取方塊，以指定要根據此資料庫的伺服器設定稽核此資料庫。 如果已經選取此選項，您會看到可讓您從這個內容檢視或修改伺服器稽核設定的 [檢視伺服器稽核設定] 連結。

    ![瀏覽窗格][2]
4. 如果您想要在資料庫層級上啟用 Blob 稽核 (在伺服器層級稽核之外額外啟用，或取代伺服器層級稽核)，請**取消選取** [從伺服器繼承稽核設定] 選項，[開啟] 稽核，然後選擇 [Blob] 稽核類型。

    ![瀏覽窗格][3]
5. 選取 [儲存體詳細資料] 以開啟 [稽核記錄儲存體] 刀鋒視窗。 選取記錄要儲存所在的 Azure 儲存體帳戶，以及將舊記錄刪除之前的保留期間，然後按一下底部的 [確定]。 **秘訣：**讓所有稽核的資料庫都使用相同的儲存體帳戶，以充分利用稽核報告範本。

    <a id="storage-screenshot"></a>
    ![瀏覽窗格][4]
6. 如果您想要自訂稽核的事件，您可以透過 PowerShell 或 REST API 來自訂。
7. 設定您的稽核設定之後，您可以開啟新的「威脅偵測」(預覽) 功能，並設定電子郵件以接收安全性警示。 「威脅偵測」可讓您接收與可能指示潛在安全性威脅的異常資料庫活動相關的主動式警示。 如需詳細資訊，請參閱[威脅偵測](sql-database-threat-detection.md)。
8. 按一下 [儲存] 。


## <a name="table-auditing"></a>資料表稽核

> [!IMPORTANT]
> 在設定 [資料表稽核] 之前，請先檢查您目前使用的是否是[下層用戶端](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。 此外，如果您有嚴格的防火牆設定，請注意在啟用 [資料表稽核] 時，[您資料庫的 IP 端點將會變更](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。
>

1. 啟動 [Azure 入口網站](https://portal.azure.com)，位址是 https://portal.azure.com。
2. 瀏覽至您想要稽核的 SQL Database / SQL Server 設定刀鋒視窗。 在 [設定] 刀鋒視窗中，選取 [稽核與威脅偵測] (*[請參閱 Blob 稽核一節中的螢幕擷取畫面](#auditing-screenshot)*)。
3. 您可以在 [資料庫稽核組態] 刀鋒視窗中，選取 [從伺服器繼承稽核設定] 核取方塊，以指定要根據此資料庫的伺服器設定稽核此資料庫。 如果已經選取此選項，您會看到可讓您從這個內容檢視或修改伺服器稽核設定的 [檢視伺服器稽核設定] 連結。

    ![瀏覽窗格][2]
4. 如果您不想從伺服器繼承稽核設定，請**取消選取** [從伺服器繼承稽核設定] 選項，[開啟] 稽核，然後選擇 [資料表] 稽核類型。

    ![瀏覽窗格][3-tbl]
5. 選取 [儲存體詳細資料] 以開啟 [稽核記錄儲存體] 刀鋒視窗。 選取記錄要儲存所在的 Azure 儲存體帳戶，以及將舊記錄刪除之前的保留期間。 **秘訣：**讓所有稽核的資料庫都使用相同的儲存體帳戶，以充分利用稽核報告範本 (*[請參閱 Blob 稽核一節中的螢幕擷取畫面](#storage-screenshot)*)。
6. 按一下 [稽核的事件]  來自訂要稽核的事件。 在 [由事件記錄] 刀鋒視窗中，按一下 [成功] 和 [失敗] 以記錄所有事件，或選擇個別的事件類別。

    ![瀏覽窗格][5]
7. 設定您的稽核設定之後，您可以開啟新的「威脅偵測」(預覽) 功能，並設定電子郵件以接收安全性警示。 「威脅偵測」可讓您接收與可能指示潛在安全性威脅的異常資料庫活動相關的主動式警示。 如需詳細資訊，請參閱[威脅偵測](sql-database-threat-detection.md)。
8. 按一下 [儲存] 。

## <a name="auditing-geo-replicated-databases"></a>稽核異地複寫資料庫

使用異地複寫資料庫時，可以在主要資料庫、次要資料庫或兩者 (需視稽核類型而定) 設定稽核。

**資料表稽核：**您可以針對兩種資料庫 (主要和次要) 在資料庫或伺服器層級上設定個別原則。

**Blob 稽核：**遵循這些指示：

1. **主要資料庫︰**在伺服器上或在資料庫本身開啟 Blob 稽核。
2. **次要資料庫：**Blob 稽核只能從主要資料庫稽核設定開啟/關閉。

   * 在主要資料庫上開啟 Blob 稽核。 必須在「主要資料庫本身」 (而不是在伺服器上) 啟用 Blob 稽核。
   * 在主要資料庫上啟用 Blob 稽核之後，它也會在次要資料庫上變成啟用狀態。

    > [!IMPORTANT]
    > 根據預設值，次要資料庫的儲存體設定會和主要資料庫上的設定完全相同，這會導致跨地區流量。 您可以透過在次要伺服器上啟用 Blob 稽核，以及在次要伺服器儲存體設定中設定本機儲存體 (這會複寫次要資料庫的儲存體位置，並導致每個資料庫將稽核記錄儲存至本機儲存體)，來避免發生這個問題。  

## <a name="viewing-blob-auditing-logs"></a>檢視 Blob 稽核記錄

Blob 稽核記錄是以 Blob 檔案集合的方式儲存在名為 "**sqldbauditlogs**" 的容器內。

如需有關 Blob 稽核記錄儲存體資料夾階層的進一步詳細資料，請參閱 [Blob 稽核記錄格式參考 (doc 檔案下載)](https://go.microsoft.com/fwlink/?linkid=829599)。

有幾種方法可以檢視 Blob 稽核記錄：

* 透過 [Azure 入口網站](https://portal.azure.com) - 開啟相關的資料庫。 在資料庫的 [稽核與威脅偵測]刀鋒視窗的頂端，按一下 [檢視稽核記錄]。

    ![瀏覽窗格][10]

    隨即開啟 [稽核記錄] 刀鋒視窗，您可以在其中檢視記錄。

    - 您可以按一下 [稽核記錄] 刀鋒視窗頂端區域中的 [篩選] 來選擇檢視特定日期
    - 您可以在由伺服器原則或資料庫原則稽核建立的稽核記錄之間切換

    ![瀏覽窗格][11]
* 使用像是 [Azure 儲存體總管](http://storageexplorer.com/)下載您的 Azure 儲存體 Blob 容器載記錄檔。

    在您將記錄檔下載到本機之後，您可以按兩下檔案來開啟、檢視及分析 SSMS 中的記錄。

* 其他方法：

   * 您可以透過 Azure 儲存體總管同時下載多個檔案 - 用滑鼠右鍵按一下特定子資料夾 (包括特定日期所有記錄檔的子資料夾) 並選擇 [另存新檔] 以儲存在本機資料夾。

       在下載幾個檔案 (或如上所述下載整天的檔案) 之後，您可以透過以下方式在本機將它們合併：

       **開啟 [SSMS] -> [檔案] -> [開啟] -> [合併擴充事件] -> 選擇要合併的所有檔案**
   * 以程式設計方式：

     * [擴充事件讀取器 C# 程式庫](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/)
     * [使用 PowerShell 查詢擴充事件檔案](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)

   * 我們已建立一個在 Azure 中執行的[範例應用程式](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)，它利用 OMS 公用 API 將 SQL 稽核記錄推送到 OMS 以供透過 OMS 儀表板取用。

## <a name="viewing-table-audit-logs"></a>檢視資料表稽核記錄

資料表稽核是以具備 **SQLDBAuditLogs** 前置詞的 Azure 儲存體資料表集合方式儲存。

如需有關資料表稽核記錄格式的進一步詳細資料，請參閱[資料表稽核記錄格式參考 (doc 檔案下載)](http://go.microsoft.com/fwlink/?LinkId=506733)。

有幾種方法可以檢視資料表稽核記錄：

* 透過 [Azure 入口網站](https://portal.azure.com) - 開啟相關的資料庫。 在資料庫的 [稽核與威脅偵測]刀鋒視窗的頂端，按一下 [檢視稽核記錄]。

    ![瀏覽窗格][10]

    隨即開啟 [稽核記錄] 刀鋒視窗，您可以在其中檢視記錄。

    * 您可以按一下 [稽核記錄] 刀鋒視窗頂端區域中的 [篩選] 來選擇檢視特定日期
    * 您可以按一下 [稽核記錄] 刀鋒視窗頂端區域中的 [在 Excel 中開啟] 來下載及檢視 Excel 格式的稽核記錄

    ![瀏覽窗格][12]

* 或者，系統會以 [可下載的 Excel 試算表](http://go.microsoft.com/fwlink/?LinkId=403540) 形式提供一個預先設定的報告範本，以協助您快速分析記錄資料。 若要在稽核記錄上使用範本，您需要 Excel 2013 (或更新版本) 和 [Power Query (http://www.microsoft.com/download/details.aspx?id=39379)。

* 您也可以使用 Power Query 將您的稽核記錄直接從 Azure 儲存體帳戶匯入至 Excel 範本。 然後您可以瀏覽稽核記錄，並根據記錄資料建立儀表板和報告。

    ![瀏覽窗格][9]

## <a name="storage-key-regeneration"></a>儲存體金鑰重新產生
在生產中，您可能會定期重新整理儲存體金鑰。 重新整理金鑰時，您需要重新儲存稽核原則。 程序如下：

1. 在 [儲存體詳細資料] 刀鋒視窗中，將 [儲存體存取金鑰] 從 [主要] 切換成 [次要]，然後按一下底部的 [確定]。 然後按一下 [稽核組態] 刀鋒視窗頂端的 [儲存]。

    ![瀏覽窗格][6]
2. 進入 [儲存體組態] 刀鋒視窗，並**重新產生**「主要存取金鑰」。

    ![瀏覽窗格][8]
3. 返回 [稽核組態] 刀鋒視窗，將 [儲存體存取金鑰] 從 [次要] 切換成 [主要]，然後按一下底部的 [確定]。 然後按一下 [稽核組態] 刀鋒視窗頂端的 [儲存]。
4. 返回 [儲存體組態] 刀鋒視窗並**重新產生**「次要存取金鑰」 (為下一個金鑰重新整理週期做準備)。


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
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

## <a name="next-steps"></a>後續步驟

* 若要使用 PowerShell 設定和管理稽核，請參閱[使用 PowerShell 設定資料庫稽核](scripts/sql-database-create-and-configure-database-powershell.md)。
* 如需稽核的概觀，請參閱[資料庫稽核](sql-database-auditing.md)。


