<properties
    pageTitle="Log Analytics 中的組態評估方案 | Microsoft Azure"
    description="當使用 Operations Manager 代理程式或 Operations Manager 管理群組時，Log Analytics 中的組態評估方案提供 System Center Operations Manager 伺服器基礎結構的目前狀態的詳細資訊。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="configuration-assessment-solution-in-log-analytics"></a>Log Analytics 中的組態評估方案

Log Analytics 中的組態評估方案透過警示和知識建議，協助您找出潛在的伺服器組態問題。

此方案需要 System Center Operations Manager。 如果您只使用直接連接的代理程式，則無法使用組態評估。

瀏覽器需要安裝 Silverlight 外掛程式，才能檢視組態評估方案中的某些資訊。

>[AZURE.NOTE] 從 2016 年 7 月 5 日開始，組態評估解決方案無法再新增至 Log Analytics 工作區，在 2016 年 8 月 1 日之後，現有的使用者就無法再使用此方案。 對於將此解決方案用於 SQL Server 或 Active Directory 的客戶，我們建議您改為使用 [SQL Server 評估](log-analytics-sql-assessment.md)、[Active Directory 評估](log-analytics-ad-assessment.md)和 [Active Directory 複寫狀態](log-analytics-ad-replication-status.md)解決方案。 對於將組態評估用於 Windows、Hyper-V 和 System Center Virtual Machine Manager 的客戶，我們建議您使用事件集合和變更追蹤功能，以便全盤檢視您環境內的任何問題。

![組態評量圖格](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

從受監視伺服器中收集組態資料，然後傳送至雲端中 OMS 服務進行處理。 會將邏輯套用至接收的資料，且雲端服務會記錄資料。 會顯示下列各方面有關伺服器處理的資料：

- **警示：** 顯示已經為受監視伺服器產生的組態相關、主動警示。 這些是由 Microsoft 客戶和支援組織 (CSS) 所撰寫的規則並參考業界最佳作法而產生
- **知識建議：** 為在您的基礎結構中發現的工作負載顯示建議的 Microsoft 知識庫文件；它們是透過機器學習，據據您的組態所提供的自動建議。
- **分析的伺服器和工作負載：** 顯示 OMS 正在監視的伺服器和工作負載。

![組態評量儀表版](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### <a name="technologies-you-can-analyze-with-configuration-assessment"></a>您可以使用組態評估來分析的技術

OMS 組態評估會分析下列工作負載：

- Windows Server 2012 和 Microsoft Hyper-V Server 2012
- Windows Server 2008 和 Windows Server 2008 R2，包括：
    - Active Directory
    - Hyper-V 主機
    - 一般作業系統
- SQL Server 2008 及更新版本
    - SQL Server Database Engine
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 和 Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 和 Lync Server 2010
- System Center 2012 SP1 – Virtual Machine Manager

對於 SQL Server，支援分析下列 32 位元和 64 位元版本：

- SQL Server 2016 - 所有版本
- SQL Server 2014 - 所有版本
- SQL Server 2008 和 2008 R2 - 所有版本

所有支援的版本上都會分析 SQL Server Database Engine。 此外，也支援在 WOW64 實作中執行的 32 位元版本 SQL Server。

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。

- 組態評估方案需要 Operations Manager。
- 您要存取組態的每部電腦上都必須有 Operations Manager 代理程式。
- 使用 [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)中的程序，將組態評估方案加入您的 OMS 工作區。  不需要進一步的組態。

## <a name="configuration-assessment-data-collection-details"></a>「組態評估」資料收集詳細資訊

組態評估會使用您已啟用的代理程式，來收集組態資料、中繼資料及狀態資料。

下表顯示組態評估的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|
|Windows|![否](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![是](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![否](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|            ![是](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![是](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| 一天兩次|

下表顯示由組態評估所收集的資料類型範例︰

|**資料類型**|**欄位**|
|---|---|
|組態|CustomerID、AgentID、EntityID、ManagedTypeID、ManagedTypePropertyID、CurrentValue、ChangeDate|
|中繼資料|BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP 位址、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime|
|State|StateChangeEventId、StateId、NewHealthState、OldHealthState、Context、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified|

## <a name="configuration-assessment-alerts"></a>組態評估警示
您可以使用 [警示] 頁面來檢視和管理來自組態評估的警示。 警示會通知您已偵測到的問題、原因及如何解決問題。 它們也提供環境中可能會造成效能問題的組態設定的相關資訊。

![警示檢視](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] 組態評估警示與 Log Analytics 中的其他警示不同。 瀏覽器需要安裝 Silverlight 外掛程式才能檢視警示。

當您在 [警示] 頁面上選取項目或項目類別時，您會看到伺服器或工作負載清單，以及套用至每個項目的警示。

![警示清單](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

每個警示都包含 Microsoft 知識庫中的文章連結。 這些文章提供有關此警示的其他資訊。

>[AZURE.TIP] 根據預設，最多會顯示 2,000 個警示。 若要檢視更多警示，請按一下警示清單上方的通知列。

您可以按一下清單中的任何項目來檢視知識庫文件，或許可協助您解決產生此警示的問題原因。

![知識庫文章](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

您可以管理警示規則來忽略特定的規則或規則類別。

![管理警示規則](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## <a name="knowledge-recommendations"></a>知識建議
當您檢視知識建議時，將會看到記錄檔搜尋結果，其中針對提供警示相關資訊的電腦和工作負載，列出建議的 Microsoft 知識庫文件。

![知識庫建議的搜尋結果](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## <a name="servers-and-workloads-analyzed"></a>分析的伺服器和工作負載
當您檢視知識建議時，將會看到記錄檔搜尋結果，其中列出 Operations Manager 中 OMS 已知的所有伺服器和工作負載。

![伺服器和工作負載](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## <a name="next-steps"></a>後續步驟

- 使用 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md) ，檢視詳細的組態評估資料。



<!--HONumber=Oct16_HO2-->


