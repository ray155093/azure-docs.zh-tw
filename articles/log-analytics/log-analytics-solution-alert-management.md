<properties
   pageTitle="Operations Management Suite (OMS) 中的警示管理解決方案 |Microsoft Azure"
   description="Log Analytics 中的警示管理方案可協助您分析環境中的所有警示。  除了合併 OMS 內產生的警示，此方案還會將連接的 System Center Operations Manager (SCOM) 管理群組中的警示匯入到 Log Analytics。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2016"
   ms.author="bwren" />


# <a name="alert-management-solution-in-operations-management-suite-(oms)"></a>Operations Management Suite (OMS) 中的警示管理解決方案

![Alert Management icon](media/log-analytics-solution-alert-management/icon.png) 警示管理方案可協助您分析環境中的所有警示。  除了合併 OMS 內產生的警示，此方案還會將連接的 System Center Operations Manager (SCOM) 管理群組中的警示匯入到 Log Analytics。  在具有多個管理群組的環境中，警示管理方案提供所有管理群組的警示合併檢視。

## <a name="prerequisites"></a>必要條件

- 若要從 SCOM 匯入警示，此方案需要您使用 [將 Operations Manager 連接到 Log Analytics](log-analytics-om-agents.md)所述的程序，在 OMS 工作區與 SCOM 管理群組之間建立連線。  


## <a name="configuration"></a>組態

使用 [加入方案](log-analytics-add-solutions.md)所述的程序，將警示管理方案加入您的 OMS 工作區。  不需要進一步的組態。

## <a name="management-packs"></a>管理組件

如果 SCOM 管理群組已連線到 OMS 工作區，當您加入此方案時，下列管理組件會安裝在 SCOM 中。  這些管理組件不需要任何設定或維護。  

- Microsoft System Center Advisor 警示管理 (Microsoft.IntelligencePacks.AlertManagement)

如需有關方案管理組件如何更新的詳細資訊，請參閱 [將 Operations Manager 連接到 Log Analytics](log-analytics-om-agents.md)。

## <a name="data-collection"></a>資料收集

### <a name="agents"></a>代理程式

下表描述此方案支援的連接來源。

| 連接的來源 | 支援 | 說明 |
|:--|:--|:--|
| [Windows 代理程式](log-analytics-windows-agents.md) | 否 | 直接 Windows 代理程式不會產生 SCOM 警示。 |
| [Linux 代理程式](log-analytics-linux-agents.md) | 否 | 直接 Linux 代理程式不會產生 SCOM 警示。 |
| [SCOM 管理群組](log-analytics-om-agents.md) | 是 | SCOM 代理程式上產生的警示會傳遞至管理群組，然後轉送到 Log Analytics。<br><br>不需要從 SCOM 代理程式直接連接到 Log Analytics。 警示資料會從管理群組轉送至 OMS 儲存機制。 |
| [Azure 儲存體帳戶](log-analytics-azure-storage.md) | 否 | SCOM 警示不會儲存在 Azure 儲存體帳戶中。 |

### <a name="collection-frequency"></a>收集頻率

OMS 內產生的警示立即可供方案使用。  警示資料每 3 分鐘從 SCOM 管理群組傳送至 Log Analytics。  

## <a name="using-the-solution"></a>使用解決方案

當您將警示管理方案加入至 OMS 工作區時，[警示管理]  圖格會新增至 OMS 儀表板。  此圖格會顯示過去 24 小時內產生的目前作用中警示數目的計數和圖形表示。  您無法變更此時間範圍。

![Alert Management tile](media/log-analytics-solution-alert-management/tile.png)

按一下 [警示管理] 圖格以開啟 [警示管理] 儀表板。  此儀表板包含下表中的資料行。  每個資料行依計數列出前十個警示，這幾個警示符合該資料行中指定範圍和時間範圍的準則。  您可以按一下資料行底部的 [查看全部]  ，或按一下資料行標頭，以執行記錄搜尋來提供完整清單。

| 欄| 說明 |
|:--|:--|
| 重大警示 | 嚴重性為「重大」的所有警示 (依警示名稱分組)。  按一下警示名稱來執行記錄搜尋，以傳回該警示的所有記錄。 |
| 警告警示 | 嚴重性為「警告」的所有警示 (依警示名稱分組)。  按一下警示名稱來執行記錄搜尋，以傳回該警示的所有記錄。 |
| 作用中的 SCOM 警示 | [已關閉]  以外任何狀態的所有 SCOM 警示 (依產生此警示的來源分組)。 |
| 所有作用中警示 | 具有任何嚴重性的所有警示 (依警示名稱分組)。 只包含 [已關閉] 以外任何狀態的 SCOM 警示。|

如果您向右捲動，儀表板會列出數個常見的查詢，按一下即可執行警示資料的 [記錄搜尋](log-analytics-log-searches.md) 。

![警示管理儀表板](media/log-analytics-solution-alert-management/dashboard.png)

## <a name="scope-and-time-range"></a>範圍和時間範圍

根據預設，警示管理方案中分析的警示範圍是來自過去 7 天內產生的所有已連線的管理群組。  

![Alert Management scope](media/log-analytics-solution-alert-management/scope.png)

- 若要變更納入分析的管理群組，請按一下儀表板頂端的 [範圍]  。  您可以選取 [全域] 以取得所有已連線的管理群組，或選取 [依管理群組] 以選取單一管理群組。

- 若要變更警示的時間範圍，請選取儀表板頂端的 [資料根據]  。  您可以選取過去 7 天、1 天或 6 小時內產生的警示。  或者，也可以選取 [自訂]  ，再指定自訂日期範圍。

## <a name="log-analytics-records"></a>Log Analytics 記錄

警示管理方案會分析 **警示**類型的任何記錄。  它也會從 SCOM 匯入警示，並針對類型為**警示**和 SourceSystem 為 **OpsManager** 的的每個警示建立對應的記錄。  這些記錄具有下表中的屬性。  

| 屬性 | 說明 |
|:--|:--|
| 類型 | *警示* |
| SourceSystem | *OpsManager* |
| AlertContext | 造成產生警示的資料項目的詳細資料 (XML 格式)。 |
| AlertDescription | 警示的詳細描述。 |
| AlertId | 警示的 GUID。 |
| AlertName | 警示的名稱。 |
| AlertPriority | 警示的優先順序層級。 |
| AlertSeverity | 警示的嚴重性層級。 |
| AlertState | 警示的最新解決狀態。 |
| LastModifiedBy | 上次修改警示的使用者名稱。 |
| ManagementGroupName | 產生警示的管理群組名稱。 |
| RepeatCount | 相同受監視物件的同一個警示自從解決後又產生的次數。 |
| ResolvedBy | 解決警示的使用者名稱。 如果尚未解決警示，則為空白。 |
| SourceDisplayName | 產生警示的監視物件的顯示名稱。 |
| SourceFullName | 產生警示的監視物件的完整名稱。 |
| TicketId | 如果 SCOM 環境與指派警示票證的程序已整合，則此值為警示的票證識別碼。  如果未指派票證識別碼，則為空白。 |
| TimeGenerated | 建立警示的日期和時間。 |
| TimeLastModified | 上次變更警示的日期和時間。 |
| TimeRaised | 產生警示的日期和時間。 |
| TimeResolved | 解決警示的日期和時間。 如果尚未解決警示，則為空白。 |

## <a name="sample-log-searches"></a>記錄搜尋範例

下表提供此方案所收集的警示記錄的記錄搜尋範例。  

| 查詢 | 說明 |
|:--|:--|
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR | 過去 24 小時期間引發的重大警示 |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR | 過去 24 小時期間引發的警告警示  |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR &#124; measure count() as Count by SourceDisplayName | 來源和過去 24 小時期間引發的作用中警示 |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed | 過去 24 小時期間引發的重大且仍在作用中的警示 |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed | 過去 24 小時期間引發但現在已關閉的警示 |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; measure count() as Count by AlertSeverity | 過去 1 天期間引發的警示 (依嚴重性分組) |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; sort RepeatCount desc | 過去 1 天期間引發的警示 (依重複計數值排序) |

## <a name="next-steps"></a>後續步驟

- 如需有關從 Log Analytics 產生的警示的詳細資料，請深入了解 [Log Analytics 中的警示](log-analytics-alerts.md) 。



<!--HONumber=Oct16_HO2-->


