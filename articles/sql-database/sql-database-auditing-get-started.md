---
title: 開始使用 SQL Database 稽核 | Microsoft Docs
description: 開始使用 SQL Database 稽核
services: sql-database
documentationcenter: ''
author: ronitr
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: CarlRabeler; ronitr; giladm

---
# <a name="get-started-with-sql-database-auditing"></a>開始使用 SQL Database 稽核
Azure SQL Database 稽核會追蹤資料庫事件並將事件寫入您 Azure 儲存體帳戶中的稽核記錄。

稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。

稽核會啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。

* [Azure SQL Database 稽核概觀]
* [設定資料庫的稽核]
* [分析稽核記錄和報告]

## <a name="<a-id="subheading-1"></a>azure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Azure SQL Database 稽核概觀
SQL Database 稽核可讓您：

* **保留** 所選事件的稽核記錄。 您可以定義要稽核的資料庫動作類別。
* **報告** 資料庫活動。 您可以使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
* **分析** 報告。 您可以尋找可疑事件、異常活動及趨勢。

**稽核方法**有兩種：

* **Blob 稽核** - 記錄會寫入 Azure Blob 儲存體。 這是一個較新的稽核方法，可提供**更高效能**、支援**更高資料粒度物件層級稽核**，以及**更符合成本效益**。
* **資料表稽核** - 記錄會寫入 Azure 資料表儲存體。

您可以依照[設定資料庫的稽核](#subheading-2)一節中的說明，針對不同類型的事件類別設定稽核。

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

可以針對特定資料庫定義稽核原則，或將稽核原則定義為預設伺服器原則。 預設伺服器稽核原則會套用至伺服器上所有現有和新建立的資料庫。

## <a name="<a-id="subheading-2"></a>set-up-auditing-for-your-database"></a><a id="subheading-2"></a>設定資料庫的稽核
下節描述使用 Azure 入口網站進行稽核的設定。

### <a name="<a-id="subheading-2-1">i.-blob-auditing</a>"></a><a id="subheading-2-1">i.Blob 稽核</a>
1. 啟動 [Azure 入口網站](https://portal.azure.com)，位址是 https://portal.azure.com。
2. 瀏覽至您想要稽核的 SQL Database / SQL Server 設定刀鋒視窗。 在 [設定] 刀鋒視窗中，選取 [稽核和威脅偵測]。
   
    <a id="auditing-screenshot"></a>
    ![導覽窗格][1]
3. 您可以在資料庫稽核組態刀鋒視窗中，選取 [從伺服器繼承稽核設定]  核取方塊，以指定要根據此資料庫的伺服器設定稽核此資料庫。 如果已經選取此選項，您將會看到可讓您從這個內容檢視或修改伺服器稽核設定的**檢視伺服器稽核設定**連結。
   
    ![導覽窗格][2]
4. 如果您想要在資料庫層級上啟用 Blob 稽核 (在伺服器層級稽核之外額外啟用，或取代伺服器層級稽核)，請**取消選取** [從伺服器繼承稽核設定] 選項，[開啟] 稽核，然後選擇 [Blob] 稽核類型。
   
   > 如果已經啟用伺服器 Blob 稽核，資料庫設定的稽核將會與伺服器 Blob 稽核並存。  
   > 
   > 
   
    ![導覽窗格][3]
5. 選取 [儲存體詳細資料] 以開啟 [稽核記錄儲存體] 刀鋒視窗。 選取將儲存記錄的 Azure 儲存體帳戶，以及將舊記錄刪除之前的保留期間，然後按一下底部的 [確定]。 **秘訣：**讓所有稽核的資料庫都使用相同的儲存體帳戶，以充分利用稽核報告範本。
   
    <a id="storage-screenshot"></a>
    ![導覽窗格][4]
6. 如果您想要自訂稽核事件，您可以透過 PowerShell 或 REST API 來自訂- 請參閱[自動化 (PowerShell / REST API)](#subheading-7) 一節以了解更多詳細資料。
7. 按一下 [儲存] 。

### <a name="<a-id="subheading-2-2">ii.-table-auditing</a>"></a><a id="subheading-2-2">ii.資料表稽核</a>
> [!NOTE]
> 在設定 [資料表稽核] 之前，請先檢查您目前使用的是否是[下層用戶端](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。 此外，如果您有嚴格的防火牆設定，請注意在啟用 [資料表稽核] 時 [您資料庫的 IP 端點將會變更](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。
> 
> 

1. 啟動 [Azure 入口網站](https://portal.azure.com)，位址是 https://portal.azure.com。
2. 瀏覽至您想要稽核的 SQL Database / SQL Server 設定刀鋒視窗。 在 [設定] 刀鋒視窗中，選取 \[稽核與威脅偵測\](*\[請參閱 Blob 稽核一節中的螢幕擷取畫面\]\(.md#auditing-screenshot\)*)。
3. 您可以在資料庫稽核組態刀鋒視窗中，選取 [從伺服器繼承稽核設定]  核取方塊，以指定要根據此資料庫的伺服器設定稽核此資料庫。 如果已經選取此選項，您將會看到可讓您從這個內容檢視或修改伺服器稽核設定的**檢視伺服器稽核設定**連結。
   
    ![導覽窗格][2]
4. 如果您不想從伺服器繼承稽核設定，請**取消選取** [從伺服器繼承稽核設定] 選項，[開啟] 稽核，然後選擇 [資料表] 稽核類型。
   
    ![導覽窗格][3-tbl]
5. 選取 [儲存體詳細資料] 以開啟 [稽核記錄儲存體] 刀鋒視窗。 選取將儲存記錄的 Azure 儲存體帳戶，以及將舊記錄刪除之前的保留期間。 **秘訣：**讓所有稽核的資料庫都使用相同的儲存體帳戶，以充分利用稽核報告範本 (*[請參閱 Blob 稽核一節中的螢幕擷取畫面](#storage-screenshot)*)。
6. 按一下 [稽核的事件]  來自訂要稽核的事件。 在 [由事件記錄] 刀鋒視窗中，按一下 [成功] 和 [失敗]以記錄所有事件，或選擇個別的事件類別。
   
   > 自訂稽核事件也可以透過 PowerShell 或 REST API 來完成- 請參閱[自動化 (PowerShell / REST API)](#subheading-7) 一節以了解更多詳細資料。
   > 
   > 
   
    ![導覽窗格][5]
7. 設定您的稽核設定之後，您可以開啟新的「威脅偵測」(預覽) 功能，並設定電子郵件以接收安全性警示。 「威脅偵測」可讓您接收與可能指示潛在安全性威脅的異常資料庫活動相關的主動式警示。 如需詳細資料，請參閱 [開始使用威脅偵測](sql-database-threat-detection-get-started.md) 。
8. 按一下 [儲存] 。

## <a name="<a-id="subheading-3"></a>analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>分析稽核記錄和報告
稽核記錄會在您於安裝期間選擇的 Azure 儲存體帳戶中彙總。

您可以使用工具 (例如 [Azure 儲存體總管](http://storageexplorer.com/)) 來探索稽核記錄。

請參閱下面 [資料表] 和 [Blob] 稽核記錄的分析細節。

### <a name="<a-id="subheading-3-1">i.-blob-auditing</a>"></a><a id="subheading-3-1">i.Blob 稽核</a>
Blob 稽核記錄是以 Blob 檔案集合的方式儲存在名為 "**sqldbauditlogs**" 的容器內。

如需有關 Blob 稽核記錄儲存體資料夾階層的進一步詳細資料，請參閱 [Blob 稽核記錄格式參考 (doc 檔案下載)](https://go.microsoft.com/fwlink/?linkid=829599)。

有幾種方法可以檢視 Blob 稽核記錄：

1. 透過 Azure 入口網站 - **請參閱下面 [資料表稽核一節](#activity-ui)中的方法 (1)** (不支援 Excel 下載)。
2. 使用像是 [Azure 儲存體總管](http://storageexplorer.com/)您的 Azure 儲存體 Blob 容器載記錄檔。
   
    在您將記錄檔下載到本機之後，您可以按兩下檔案來開啟、檢視及分析 SSMS 中的記錄。
   
    其他方法：
   
   * 您可以透過 Azure 儲存體總管**同時下載多個檔案** - 用滑鼠右鍵按一下特定子資料夾 (例如，包括特定日期所有記錄檔的子資料夾) 並選擇 [另存新檔] 以儲存在本機資料夾。
     
       在下載幾個檔案 (或如上所述下載整天的檔案) 之後，您可以透過以下方式在本機將它們合併：
     
       **開啟 [SSMS] -> [檔案] -> [開啟] -> [合併擴充事件] -> 選擇要合併的所有檔案**
   * 以程式設計方式：
     
     * 擴充事件讀取器 **C# 程式庫** ([這裡提供更多資訊](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/))
     * 使用 **PowerShell** 查詢擴充事件檔案 ([這裡提供更多資訊](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/))

### <a name="<a-id="subheading-3-2">ii.-table-auditing</a>"></a><a id="subheading-3-2">ii.資料表稽核</a>
資料表稽核檔是以具備 **SQLDBAuditLogs** 前置詞的 Azure 儲存體資料表集合方式儲存。

如需有關資料表稽核記錄格式的進一步詳細資料，請參閱[資料表稽核記錄格式參考 (doc 檔案下載)](http://go.microsoft.com/fwlink/?LinkId=506733)。

有幾種方法可以檢視資料表稽核記錄：

<a id="activity-ui"></a>

1. 透過 [Azure 入口網站](https://portal.azure.com) - 在 [稽核與威脅偵測] 刀鋒視窗最上面，按一下 [更多]，然後按一下 [總管]。 就會開啟一個 [稽核記錄] 刀鋒視窗，您可以在其中檢視記錄。
   
   * 您可以按一下 [稽核記錄] 刀鋒視窗頂端區域中的 [篩選] 來選擇檢視特定日期
   * 您可以按一下 [稽核記錄] 刀鋒視窗頂端區域中的 [在 Excel 中開啟] 來下載及檢視 Excel 格式的稽核記錄
     
     ![導覽窗格][7]
2. 或者，系統會以 [可下載的 Excel 試算表](http://go.microsoft.com/fwlink/?LinkId=403540) 形式提供一個預先設定的報告範本，以協助您快速分析記錄資料。 若要在稽核記錄上使用範本，您需要 Excel 2013 (或更新版本) 和 Power Query (您可從 [此處](http://www.microsoft.com/download/details.aspx?id=39379)下載)。
   
    您也可以使用 Power Query 將您的稽核記錄直接從 Azure 儲存體帳戶匯入至 Excel 範本。 然後您可以瀏覽稽核記錄，並根據記錄資料建立儀表板和報告。
   
    ![導覽窗格][9]

## <a name="<a-id="subheading-5"></a>practices-for-usage-in-production"></a><a id="subheading-5"></a>生產環境中的使用方式作法
<!--The description in this section refers to screen captures above.-->

### <a name="<a-id="subheading-6">auditing-geo-replicated-databases</a>"></a><a id="subheading-6">稽核異地複寫資料庫</a>
使用異地複寫資料庫時，可以在主要資料庫、次要資料庫或兩者 (需視稽核類型而定) 設定稽核。

**資料表稽核** - 您可以依照[設定資料庫的稽核](#subheading-2-2)一節所述，針對兩種資料庫 (主要和次要) 在資料庫或伺服器層級上設定個別原則。

**Blob 稽核**遵循這些指示：

1. **主要資料庫** - 依照[設定資料庫的稽核](#subheading-2-1)一節所述，在伺服器或資料庫本身開啟 **Blob 稽核**。
2. **次要資料庫** - Blob 稽核只能從主要資料庫稽核設定開啟/關閉。
   
   * 依照[設定資料庫的稽核](#subheading-2-1)一節所述，在**主要資料庫**上開啟 **Blob 稽核**。 (**注意：** 必須在資料庫本身 (而不是在伺服器上) 啟用 Blob 稽核)。
   * 在主要資料庫上啟用 Blob 稽核之後，它也會在次要資料庫上變成啟用狀態。
   * **重要：**依照預設，次要資料庫的**儲存體設定**將會和主要資料庫上的設定完全相同，這會導致**跨地區流量**。 您可以透過在 [次要伺服器]中啟用 Blob 稽核，以及在次要伺服器儲存體設定中設定 [本機儲存體](這將會複寫次要資料庫的儲存體位置，並導致每個資料庫將稽核記錄儲存至本機儲存體.md)，來避免發生這個問題。  

<br>

### <a name="<a-id="subheading-6">storage-key-regeneration</a>"></a><a id="subheading-6">儲存體金鑰重新產生</a>
在生產中，您可能會定期重新整理儲存體金鑰。 重新整理金鑰時，您需要重新儲存稽核原則。 程序如下：

1. 在 [儲存體詳細資料] 刀鋒視窗中，將 [儲存體存取金鑰] 從 [主要] 切換成 [次要]，然後按一下底部的 [確定]。 然後按一下 [稽核組態] 刀鋒視窗頂端的 [儲存]。
   
    ![導覽窗格][6]
2. 進入 [儲存體組態] 刀鋒視窗，並**重新產生***主要存取金鑰*。
   
    ![導覽窗格][8]
3. 返回 [稽核組態] 刀鋒視窗，將 [儲存體存取金鑰] 從 [次要] 切換成 [主要]，然後按一下底部的 [確定]。 然後按一下 [稽核組態] 刀鋒視窗頂端的 [儲存]。
4. 返回 [儲存體組態] 刀鋒視窗並**重新產生**「次要存取金鑰」 (為下一個金鑰重新整理週期做準備)。

## <a name="<a-id="subheading-7"></a>automation-(powershell-/-rest-api)"></a><a id="subheading-7"></a>自動化 (PowerShell / REST API)
您也可以使用以下自動化工具在 Azure SQL Database 中設定稽核：

1. **PowerShell Cmdlet**
   
   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]
2. **REST API - Blob 稽核**
   
   * [建立或更新資料庫 Blob 稽核原則](https://msdn.microsoft.com/en-us/library/azure/mt695939.aspx)
   * [建立或更新伺服器 Blob 稽核原則](https://msdn.microsoft.com/en-us/library/azure/mt771861.aspx)
   * [取得資料庫 Blob 稽核原則](https://msdn.microsoft.com/en-us/library/azure/mt695938.aspx)
   * [取得伺服器 Blob 稽核原則](https://msdn.microsoft.com/en-us/library/azure/mt771860.aspx)
   * [取得伺服器 Blob 稽核操作結果](https://msdn.microsoft.com/en-us/library/azure/mt771862.aspx)
3. **REST API - 資料表稽核**
   
   * [建立或更新資料庫稽核原則](https://msdn.microsoft.com/en-us/library/azure/mt604471.aspx)
   * [建立或更新伺服器稽核原則](https://msdn.microsoft.com/en-us/library/azure/mt604383.aspx)
   * [取得資料庫稽核原則](https://msdn.microsoft.com/en-us/library/azure/mt604381.aspx)
   * [取得伺服器稽核原則](https://msdn.microsoft.com/en-us/library/azure/mt604382.aspx)

<!--Anchors-->
[Azure SQL Database 稽核概觀]: #subheading-1
[設定資料庫的稽核]: #subheading-2
[分析稽核記錄和報告]: #subheading-3
[生產環境中的使用方式作法]: #subheading-5
[儲存體金鑰重新產生]: #subheading-6
[自動化 (PowerShell / REST API)]: #subheading-7


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


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx



<!--HONumber=Oct16_HO2-->


