<properties
    pageTitle="Log Analytics 中的系統更新評估方案 | Microsoft Azure"
    description="您可以使用 Log Analytics 中的系統更新方案，協助您將遺失的更新套用到基礎結構中的伺服器。"
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
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="system-update-assessment-solution-in-log-analytics"></a>Log Analytics 中的系統更新評估方案

您可以使用 Log Analytics 中的系統更新方案，協助您將遺失的更新套用到基礎結構中的伺服器。 安裝此方案之後，您可以在 OMS 的 [概觀] 頁面上使用 [系統更新評估] 圖格，檢視受監視伺服器缺少的更新。

如果找到遺失的更新，[更新] 儀表板上會顯示詳細資料。 您可以使用 [ **更新** ] 儀表板處理遺失的更新及開發計劃，將它們套用至所需要的伺服器。

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。

- 使用 [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)所述的程序，將系統更新評估方案加入您的 OMS 工作區。  不需要進一步的組態。

## <a name="system-update-data-collection-details"></a>「系統更新」資料收集詳細資訊

系統更新評估會使用您已啟用的代理程式，來收集中繼資料和狀態資料。

下表顯示系統更新評估的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|
|Windows|![是](./media/log-analytics-system-update/oms-bullet-green.png)|![是](./media/log-analytics-system-update/oms-bullet-green.png)|![否](./media/log-analytics-system-update/oms-bullet-red.png)|            ![否](./media/log-analytics-system-update/oms-bullet-red.png)|![是](./media/log-analytics-system-update/oms-bullet-green.png)| 安裝更新之後每天至少 2 次且持續 15 分鐘|

下表顯示由系統更新評估所收集的資料類型範例︰

|**資料類型**|**欄位**|
|---|---|
|中繼資料|BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP 位址、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime|
|State|StateChangeEventId、StateId、NewHealthState、OldHealthState、Context、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified|


### <a name="to-work-with-updates"></a>使用更新

1. 在 [概觀] 頁面上，按一下 [系統更新評估] 圖格。  
    ![系統更新評估圖格](./media/log-analytics-system-update/sys-update-tile.png)
2. 在 [ **更新** ] 儀表板上，檢視更新類別。  
    ![更新儀表板](./media/log-analytics-system-update/sys-updates02.png)
3. 捲動到頁面的右邊，以檢視 [Windows 重大/安全性更新] 刀鋒視窗，然後在 [分類] 下按一下 [安全性更新]。  
    ![安全性更新](./media/log-analytics-system-update/sys-updates03.png)
4. 在 [記錄檔搜尋] 頁面上，會顯示各種有關基礎結構中伺服器缺少之安全性的更新資訊。 按一下 [清單] 檢視有關更新的詳細資訊。  
    ![搜尋結果 - 清單](./media/log-analytics-system-update/sys-updates04.png)
5. 在 [記錄檔搜尋] 頁面中，會顯示有關各個更新的詳細資訊。 在 KBID 編號旁邊，按一下 [檢視] 可檢視在 Microsoft 支援服務網站上的對應章。  
    ![記錄檔搜尋結果 - 檢視 KB](./media/log-analytics-system-update/sys-updates05.png)
6. 您的網頁瀏覽器會在新索引標籤中開啟此更新的 Microsoft 支援服務網頁。 檢視缺少之更新的相關資訊。  
    ![Microsoft 支援服務網頁](./media/log-analytics-system-update/sys-updates06.png)
7. 使用您找到的資訊，您可以建立計畫以手動方式套用缺少的更新，或者您可以繼續剩下的步驟自動套用更新。
8. 如果您想要自動套用缺少的更新，請回到 [更新] 儀表板，然後在 [更新執行] 之下，按一下 [按一下以排程更新的執行]。  
    ![更新執行 - 已排程索引標籤](./media/log-analytics-system-update/sys-updates07.png)
9. 在 [更新執行] 頁面的 [排程] 索引標籤中，按一下 [新增] 來建立新的更新執行。  
    ![已排程索引標籤 - 新增](./media/log-analytics-system-update/sys-updates08.png)
10. 在 [新增更新執行] 頁面上，輸入更新執行的名稱、新增個別的電腦或電腦群組、定義排程，然後按一下 [儲存]。  
    ![新增更新執行](./media/log-analytics-system-update/sys-updates09.png)
11. [更新執行] 頁面的 [排程] 索引標籤中，會顯示您已排程的新更新執行。  
    ![已排程索引標籤](./media/log-analytics-system-update/sys-updates10.png)
12. 當更新執行開始執行，在 [執行中] 索引標籤中可以看到其資訊。  
    ![執行中索引標籤](./media/log-analytics-system-update/sys-updates11.png)
13. 更新執行完成之後，[已完成] 索引標籤會顯示狀態。
14. 如果是從更新執行套用更新，您會看到 [Windows 重大/安全性更新] 刀鋒視窗中的更新數目減少。  
    ![Windows 重大/安全性更新刀鋒視窗 - 更新計數減少](./media/log-analytics-system-update/sys-updates12.png)



## <a name="next-steps"></a>後續步驟

- [搜尋記錄檔](log-analytics-log-searches.md) 以檢視詳細的系統更新資料。



<!--HONumber=Oct16_HO2-->


