---
title: "Operations Management Suite (OMS) 中的服務對應 | Microsoft Docs"
description: "服務對應是一個 Operations Management Suite (OMS) 解決方案，可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。  本文會詳細說明如何在環境中部署服務對應並將它用於各種案例。"
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: 638410921c6dad72e1bbe0c035243cea70a3deb1
ms.openlocfilehash: 4bab1ba9c30cee50baeddc06931a3997aac0f33f
ms.lasthandoff: 02/16/2017


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>在 Operations Management Suite (OMS) 中使用服務對應解決方案
服務對應可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 如果您想到您的伺服器，ADM 可讓您以互連系統 (提供重要服務) 的形式檢視它們。  不需要進行任何設定，只要安裝了代理程式，服務對應就會顯示橫跨任何 TCP 連接架構的伺服器、處理程序和連接埠之間的連接。

本文說明使用服務對應的詳細資訊。  如需設定服務對應和啟用代理程式的相關資訊，請參閱[在 Operations Management Suite (OMS) 中設定服務對應解決方案](operations-management-suite-service-map-configure.md)


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>使用案例︰讓 IT 處理序可以感知相依性

### <a name="discovery"></a>探索
服務對應會自動建置跨伺服器、處理程序和協力廠商服務的一般相依性參考對應。  它會探索並對應所有 TCP 相依性，其中會識別非預期的連線、您倚賴的遠端協力廠商系統，以及與傳統網路暗區 (例如 Active Directory) 的相依性。  服務對應可探索到受管理系統嘗試進行的失敗網路連線，幫助您識別潛在的伺服器錯誤設定、服務中斷和網路問題。

### <a name="incident-management"></a>事件管理
服務對應可藉由顯示系統的連線方式及其如何互相影響，協助您免去推敲問題所在的工作。  除了失敗的連線，已連線用戶端的相關資訊還可協助識別設定錯誤的負載平衡器、非預期或過度負載的重要服務，以及與生產系統通訊的開發人員機器等惡意用戶端。  具有 OMS 變更追蹤的整合工作流程也可讓您查看後端機器或服務上的變更事件是否能夠說明事件的根本原因。

### <a name="migration-assurance"></a>移轉保證
服務對應可讓您有效地規劃、加速和驗證 Azure 移轉，確保沒有遺漏任何項目，且不會發生意外中斷。  您可以探索所有需要一起移轉的交互相依系統、評估系統組態和容量，以及識別執行中的系統是否仍為使用者提供服務或是應該解除委任而非移轉。  移動完成之後，您可以檢查用戶端負載和身分識別，以確認測試系統和客戶有連線。  如果子網路規劃和防火牆定義有問題，「服務對應」對應中的失敗連線會向您指出需要連線的系統。

### <a name="business-continuity"></a>業務持續性
如果您使用 Azure Site Recovery，且需要協助以定義應用程式環境的復原順序，服務對應可以自動向您顯示系統彼此間的相依情形，以確保復原計畫可靠。  藉由選擇重要伺服器並檢視其用戶端，您可以識別應該在重要伺服器還原並可用之後才能復原的前端系統。  相反地，藉由查看重要伺服器的後端相依性，您可以識別必須在焦點系統還原之前先復原的系統。

### <a name="patch-management"></a>修補程式管理
服務對應藉由向您指出其他哪些小組和伺服器依賴您的服務，增強了 OMS 系統更新評估的使用性，因此您可以事先通知他們，然後才關閉系統進行修補。  服務對應更藉由向您指出服務在修補並重新啟動之後是否可用並已正確連線，增強了 OMS 中的修補程式管理。


## <a name="mapping-overview"></a>對應概觀
服務對應代理程式會收集其安裝所在之伺服器上所有 TCP 連線處理程序的相關資訊，以及有關每個處理程序之輸入和輸出連線的詳細資料。  使用服務對應解決方案左邊的 [機器清單]，即可選取具有服務對應代理程式的機器，以便視覺化呈現這些機器在所選時間範圍內的相依性。  機器相依性對應的焦點會集中在特定機器，並顯示所有屬於該機器之直接 TCP 用戶端或伺服器的所有機器。

![服務對應概觀](media/oms-service-map/service-map-overview.png)

對應內的機器可以展開，以顯示所選時間範圍內具有作用中網路連線的執行中處理程序。  展開具有服務對應代理程式的遠端機器以顯示處理程序詳細資料時，只會顯示與焦點機器通訊的程序。  連線到焦點機器的無代理程式前端機器計數，會在這些前端機器所連線到的處理程序左邊指出。  如果焦點機器進行無代理程式的後端電腦連接，該後端伺服器連同其他連接至相同連接埠號碼的連線會被併入伺服器連接埠群組。

根據預設，「服務對應」對應會顯示過去 10 分鐘的相依性資訊。  使用左上角的時間控制項，即可查詢對應的過往時間範圍 (最長可達一小時)，以顯示相依性的過往情形，例如在事件發生期間或變更發生之前。    付費工作區的服務對應資料會儲存 30 天，免費工作區的服務對應資料則會儲存 7 天。

## <a name="status-badges"></a>狀態徽章
在對應中每一部伺服器的底部，可以是一串狀態徽章，傳達伺服器的相關狀態資訊。  徽章表示在其中一個 OMS 解決方案整合中，有某些伺服器的相關資訊。  按一下徽章，會在右窗格中直接顯示狀態的詳細資料。  目前可使用的狀態徽章包括警示、變更、安全性和更新。

![失敗的連線](media/oms-service-map/status-badges.png)

## <a name="failed-connections"></a>失敗的連線
「服務對應」對應內會顯示處理程序和電腦的失敗連線，並以紅色虛線顯示用戶端系統是否無法連線到處理程序或連接埠。  已部署服務對應代理程式的系統如果就是嘗試進行失敗連線的系統，則會報告失敗的連線。  服務對應會觀察無法建立連線的 TCP 通訊端來測量此失敗連線。  連線失敗可能是因為防火牆，用戶端或伺服器設定不正確，或遠端服務無法使用。

![失敗的連線](media/oms-service-map/failed-connections.png)

了解失敗的連線有助於疑難排解、移轉驗證、安全性分析和整體架構理解。  失敗的連線有時無害，但它們通常會直指問題，例如容錯移轉環境突然變成無法連線，...或兩個應用程式層在雲端移轉之後無法通訊。

## <a name="client-groups"></a>用戶端群組
用戶端群組是對應上的方塊，代表沒有相依性代理程式的用戶端電腦。  單一用戶端群組代表個別處理序的用戶端。

![用戶端群組](media/oms-service-map/client-groups.png)

若要查看用戶端群組中伺服器的 IP 位址，選取群組。  群組的內容將列在 [屬性] 面板中。

![用戶端群組屬性](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>伺服器連接埠群組
伺服器連接埠群組是方塊，代表沒有相依性代理程式的伺服器上的伺服器連接埠。  方塊會列出伺服器連接埠，以及與該連接埠連線的伺服器數目。  展開方塊可查看個別伺服器和連線。  如果在方塊中只有一部伺服器，則會列出其名稱或 IP 位址。

![伺服器連接埠群組](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>內容功能表
按一下任何伺服器右上角的三個點，會顯示該伺服器的內容功能表。

![失敗的連線](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>載入伺服器對應
[載入伺服器對應] 會導覽至新的對應，以所選取的伺服器做為新的焦點機器。

### <a name="showhide-self-links"></a>顯示/隱藏自我連結
[顯示自我連結] 將會重繪包括任何自我連結的伺服器節點，即處理序在該伺服器上開始和結束的 TCP 連線。  如果沒有顯示自我連結，功能表會變為 [隱藏自我連結]，讓使用者可以開關自我連結的繪圖。

## <a name="computer-summary"></a>電腦摘要
[機器摘要] 面板包含伺服器作業系統概觀、相依性計數及其他 OMS 解決方案中的各種資料，包括效能標準、變更追蹤、安全性及更新等。

![機器摘要](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>電腦和處理序屬性
在瀏覽「服務對應」對應時，您可以選取機器和處理程序來取得其他有關其屬性的內容。  機器會提供有關 DNS 名稱、IPv4 位址、CPU 和記憶體容量、VM 類型、作業系統版本、上次重新開機時間，以及其 OMS 和服務對應代理程式識別碼的資訊。

![機器屬性](media/oms-service-map/machine-properties.png)

處理序的詳細資料是收集自作業系統中繼資料，其內容與執行中的處理序有關，包括處理序名稱、處理序描述、使用者名稱和網域 (在 Windows 上)、公司名稱、產品名稱、產品版本、工作目錄、命令列，以及處理序開始時間。

![處理程序屬性](media/oms-service-map/process-properties.png)

「處理程序摘要」面板會提供其他有關該處理程序連線的資訊，包括其繫結連接埠、輸入及輸出連線，以及失敗的連線。

![處理程序摘要](media/oms-service-map/process-summary.png)

## <a name="oms-alerts-integration"></a>OMS 警示整合
服務對應會與 OMS 警示整合，以顯示所選時間範圍內針對所選伺服器觸發的警示。  如果有最新警示，伺服器會顯示一個圖示，且 [機器警示] 面板會列出警示

![機器警示面板](media/oms-service-map/machine-alerts.png)

請注意，要使服務對應顯示相關警示，必須建立警示規則，才能針對特定的電腦觸發。  若要建立適當的警示︰
- 包含依電腦群組的子句："by Computer interval 1minute" (依 1 分鐘的電腦間隔)
- 選擇依據標準度量警示

![警示組態](media/oms-service-map/alert-configuration.png)


## <a name="oms-log-events-integration"></a>OMS 記錄事件整合
服務對應會與記錄搜尋整合，以顯示所選時間範圍期間，適用於所選伺服器的所有可用記錄事件計數。  您可以按一下事件計數清單中的任一列，以移至記錄搜尋並查看個別的記錄事件。

![記錄事件](media/oms-service-map/log-events.png)

## <a name="oms-change-tracking-integration"></a>OMS 變更追蹤整合
當「服務對應」和「變更追蹤」這兩個解決方案皆已在 OMS 工作區中啟用並設定，便會自動進行整合。

「機器變更追蹤」面板會顯示所有變更的清單，從最新排到最舊，並有連結可供向下鑽研記錄檔搜尋，以取得其他詳細資料。
![機器變更追蹤面板](media/oms-service-map/change-tracking.png)

以下是在選取 [在 Log Analytics 中顯示] 後組態變更事件的向下鑽研檢視。
![組態變更事件](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>OMS 效能整合
[機器效能] 面板會顯示所選伺服器標準的效能標準。  這些標準包含 CPU 使用率、記憶體使用量、傳送和接收的網路位元組及按照傳送和接收的網路位元組排序的前幾個處理序清單。
![機器變更追蹤面板](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>OMS 安全性整合
當「服務對應」和「安全性與稽核」這兩個解決方案皆已在 OMS 工作區中啟用並設定，便會自動進行整合。

[機器安全性] 面板會顯示 OMS 安全性與稽核解決方案中針對所選伺服器的資料。  此面板會列出所選時間範圍內伺服器任何未處理之安全性問題的摘要。  按一下任一安全性問題會向下切入到記錄搜尋，顯示關於安全性問題的詳細資料。
![機器變更追蹤面板](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>OMS 更新整合
當「服務對應」和「更新管理」這兩個解決方案皆已在 OMS 工作區中啟用並設定，便會自動進行整合。

[機器更新] 面板會顯示 OMS 更新管理解決方案中針對所選伺服器的資料。  此面板會列出所選時間範圍內伺服器所缺少之任何更新的摘要。
![機器變更追蹤面板](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics 記錄
服務對應的電腦和處理程序清查資料可供在 Log Analytics 中進行[搜尋](../log-analytics/log-analytics-log-searches.md)。  這適用於包括移轉規劃、容量分析、探索和臨機操作效能疑難排解在內的案例。

除了當處理序或電腦啟動時或是新增到服務對應時所產生的記錄外，每小時還會為每個唯一的電腦和處理序產生一個記錄。  這些記錄具有下表中的屬性。  ServiceMapComputer_CL 事件中的欄位和值對應到 ServiceMap ARM API 中的機器資源的欄位。  ServiceMapProcess_CL 事件中的欄位和值對應到 ServiceMap ARM API 中的處理序資源的欄位。  ResourceName_s 欄位符合相對應的 ARM 資源中的名稱欄位。 附註 - 隨著服務對應的功能增加，這些欄位可能會跟著變更。


有可用來識別唯一處理程序和電腦的內部產生屬性︰

- 電腦 - 使用 ResourceId 或 ResourceName_s 來唯一識別 OMS 工作區中的電腦。
- 處理序 - 使用 ResourceId 來唯一識別 OMS 工作區中的處理序。 ResourceName_s 在執行處理序的機器 (MachineResourceName_s) 的環境中是唯一的 

因為在指定時間範圍內可以有多筆指定處理序和電腦的記錄，查詢可能會傳回相同電腦或處理序的多筆記錄。 若只要包含最新的記錄，請在查詢中加入加入 "| dedup ResourceId"。

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL 記錄
類型為 **ServiceMapComputer_CL** 的記錄會有伺服器 (具有服務對應代理程式) 的清查資料。  這些記錄具有下表中的屬性：

| 屬性 | 說明 |
|:--|:--|
| 類型 | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 工作區中機器的唯一識別碼 |
| ResourceName_s | 工作區中機器的唯一識別碼 |
| ComputerName_s | 電腦 FQDN |
| Ipv4Addresses_s | 伺服器的 IPv4 位址清單 |
| Ipv6Addresses_s | 伺服器的 IPv6 位址清單 |
| DnsNames_s | DNS 名稱的陣列 |
| OperatingSystemFamily_s | windows 或 linux |
| OperatingSystemFullName_s | 作業系統的完整名稱  |
| Bitness_s | 機器的位元 (32 位元或&64; 位元) |
| PhysicalMemory_d | 實體記憶體 (MB) |
| Cpus_d | CPU 數目 |
| CpuSpeed_d | CPU 速度 (MHz)|
| VirtualizationState_s | "unknown"、"physical"、"virtual"、"hypervisor" |
| VirtualMachineType_s | "hyperv"、"vmware" 等 |
| VirtualMachineNativeMachineId_g | VM 識別碼 (由其 Hypervisor 指派) |
| VirtualMachineName_s | VM 名稱 |
| BootTime_t | 開機時間 |



### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 類型記錄
類型為 **ServiceMapProcess_CL** 的記錄會有伺服器 (具有服務對應代理程式) 上 TCP 連線處理程序的清查資料。  這些記錄具有下表中的屬性：

| 屬性 | 說明 |
|:--|:--|
| 類型 | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 工作區中處理序的唯一識別碼 |
| ResourceName_s | 在執行處理序的機器上，處理序的唯一識別碼|
| MachineResourceName_s | 機器資源名稱 |
| ExecutableName_s | 處理序執行檔名稱 |
| StartTime_t | 處理序集區的開始時間 |
| FirstPid_d | 處理序集區中的第一個 pid |
| Description_s | 處理序的描述 |
| CompanyName_s | 公司名稱 |
| InternalName_s | 內部名稱 |
| ProductName_s | 產品名稱 |
| ProductVersion_s | 產品版本 |
| FileVersion_s | 檔案版本 |
| CommandLine_s | 命令列 |
| ExecutablePath _s | 執行檔的路徑 |
| WorkingDirectory_s | 工作目錄 |
| UserName | 執行處理序的帳戶 |
| UserDomain | 執行處理序的網域 |


## <a name="sample-log-searches"></a>記錄檔搜尋範例

### <a name="list-all-known-machines"></a>列出所有已知的機器
Type=ServiceMapComputer_CL | dedup ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有受管理電腦的實體記憶體容量。
Type=ServiceMapComputer_CL | select PhysicalMemory_d, ComputerName_s | Dedup ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>列出電腦名稱、DNS、IP 和 OS。
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s  | dedup ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>在命令列中尋找具有「sql」的所有處理程序
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>以資源名稱尋找機器 (最新的記錄)
Type=ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>以 IP 位址尋找機器 (最新的記錄)
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-given-machine"></a>列出指定機器上的所有已知處理序
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>列出所有執行 SQL 的電腦
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>資料中心內所有唯一 curl 產品版本的清單
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>為所有執行 CentOS 的電腦建立電腦群組
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="rest-api"></a>REST API
服務對應中所有的伺服器、處理序及相依性資料，都可透過[服務對應 REST API](https://docs.microsoft.com/en-us/rest/api/servicemap/) 取得。


## <a name="diagnostic-and-usage-data"></a>診斷和使用量資料
當您使用服務對應服務時，Microsoft 會自動收集使用量和效能資料。 Microsoft 使用這項資料來提供和改進服務對應服務的品質、安全性和完整性。 資料中除了包含軟體 (如作業系統) 組態和版本的相關資訊，還包含 IP 位址、DNS 名稱和工作站名稱，以便提供精確且有效率的疑難排解功能。 我們不會收集姓名、地址或其他連絡資訊。

如需有關資料收集與使用方式的詳細資訊，請參閱 [Microsoft 線上服務隱私權聲明](https://go.microsoft.com/fwlink/?LinkId=512132)。


## <a name="next-steps"></a>後續步驟
- 深入了解 Log Analytics 中的[記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)，以擷取服務對應所收集的資料。


## <a name="feedback"></a>意見反應
您對「服務對應」或這份文件有任何意見反應要提供給我們嗎？  請瀏覽我們的 [User Voice 頁面](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，您可以在此頁面提出功能建議或對現有的建議進行投票。

