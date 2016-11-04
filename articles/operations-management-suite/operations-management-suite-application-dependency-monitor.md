---
title: Operations Management Suite (OMS) 中的應用程式相依性監視 (ADM) | Microsoft Docs
description: 應用程式相依性監視 (ADM) 是一個 Operations Management Suite (OMS) 解決方案，可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。  本文會詳細說明如何在環境中部署 ADM 並將它用於各種案例。
services: operations-management-suite
documentationcenter: ''
author: daseidma
manager: jwhit
editor: tysonn

ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2016
ms.author: daseidma;bwren

---
# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-(oms)"></a>在 Operations Management Suite (OMS) 中使用應用程式相依性監視解決方案
![Alert Management icon](media/operations-management-suite-application-dependency-monitor/icon.png) 應用程式相依性監視 (ADM) 可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 如果您想到您的伺服器，ADM 可讓您以互連系統 (提供重要服務) 的形式檢視它們。  不需要進行任何設定，只要安裝了代理程式，應用程式相依性監視就會顯示橫跨任何 TCP 連接架構的伺服器、處理程序和連接埠之間的連接。

本文會說明使用應用程式相依性監視的詳細資料。  如需設定 ADM 和啟用代理程式的相關資訊，請參閱[在 Operations Management Suite (OMS) 中設定應用程式相依性監視解決方案](operations-management-suite-application-dependency-monitor-configure.md)

> [!NOTE]
> 應用程式相依性監視目前僅供私人預覽。  您可以在 [https://aka.ms/getadm](https://aka.ms/getadm) 要求存取 ADM 私人預覽。
> 
> 在私人預覽期間，所有 OMS 帳戶都可無限制地存取 ADM。  ADM 節點是免費的，但和其他任何解決方案一樣，AdmComputer_CL 和 AdmProcess_CL 類型的 Log Analytics 資料會進行計量。
> 
> 當 ADM 進入公開預覽後，將會僅供 OMS 定價方案中免費和付費的深入解析與分析客戶使用。  免費層帳戶僅限使用 5 個 ADM 節點。  如果您正在參與私人預覽，而且當 ADM 進入公開預覽時並未註冊 OMS 定價方案，ADM 屆時將會停用。 
> 
> 

## <a name="use-cases:-make-your-it-processes-dependency-aware"></a>使用案例︰讓 IT 處理程序可以感知相依性
### <a name="discovery"></a>探索
ADM 會自動建置跨伺服器、處理程序和第 3 方服務的一般相依性參考對應。  它會探索並對應所有 TCP 相依性，進而識別非預期的連線、您仰賴的遠端第 3 方系統，以及與傳統網路暗區 (如 DNS 和 AD) 的相依性。  ADM 可探索到受管理系統嘗試進行的失敗網路連線，幫助您識別潛在的伺服器錯誤設定、服務中斷和網路問題。

### <a name="incident-management"></a>事件管理
ADM 可藉由顯示系統的連線方式及其如何互相影響，協助您免去推敲問題所在的工作。  除了失敗的連線，已連線用戶端的相關資訊還可協助識別設定錯誤的負載平衡器、非預期或過度負載的重要服務，以及與生產系統通訊的開發人員機器等惡意用戶端。  具有 OMS 變更追蹤的整合工作流程也可讓您查看，後端機器或服務上的變更事件是否能夠說明事件的根本原因。

### <a name="migration-assurance"></a>移轉保證
ADM 可讓您有效地規劃、加速和驗證 Azure 移轉，確保沒有遺漏任何項目，且不會發生意外中斷。  您可以探索所有需要一起移轉的交互相依系統、評估系統組態和容量，以及識別執行中的系統是否仍為使用者提供服務或是應該解除委任而非移轉。  移動完成之後，您可以檢查用戶端負載和身分識別，以確認測試系統和客戶有連線。  如果子網路規劃和防火牆定義有問題，ADM 對應中的失敗連線會向您指出需要連線的系統。

### <a name="business-continuity"></a>商務持續性
如果您使用 Azure Site Recovery，且需要協助以定義應用程式環境的復原順序，ADM 可以自動向您顯示系統彼此間的相依情形，以確保復原計畫可靠。  藉由選擇重要伺服器並檢視其用戶端，您可以識別應該在重要伺服器還原並可用之後才能復原的前端系統。  相反地，藉由查看重要伺服器的後端相依性，您可以識別必須在焦點系統還原之前先復原的系統。

### <a name="patch-management"></a>修補程式管理
ADM 藉由向您指出其他哪些小組和伺服器依賴您的服務，增強了 OMS 系統更新評估的使用性，因此您可以事先通知他們，然後才關閉系統進行修補。  ADM 更藉由向您指出服務在修補並重新啟動之後是否可用並已正確連線，增強了 OMS 中的修補程式管理。 

## <a name="mapping-overview"></a>對應概觀
ADM 代理程式會收集其安裝所在之伺服器上所有 TCP 連線處理程序的相關資訊，以及有關每個處理程序之輸入和輸出連線的詳細資料。  使用 ADM 解決方案左邊的 [機器清單]，即可選取具有 ADM 代理程式的機器，以便視覺化呈現這些機器在所選時間範圍內的相依性。  機器相依性對應的焦點會集中在特定機器，並顯示所有屬於該機器之直接 TCP 用戶端或伺服器的所有機器。

![ADM 概觀](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

對應內的機器可以展開，以顯示所選時間範圍內具有作用中網路連線的執行中處理程序。  展開具有 ADM 代理程式的遠端機器以顯示處理程序詳細資料時，只會顯示與焦點機器通訊的程序。  連線到焦點機器的無代理程式前端機器計數，會在這些前端機器所連線到的處理程序左邊指出。  如果焦點機器正在連線到後端機器但未使用代理程式，該後端在對應內會以顯示其 IPv4 位址的節點來代表，而且您可以展開該節點，以顯示焦點機器所連絡的個別連接埠和服務。

根據預設，ADM 對應會顯示過去 10 分鐘的相依性資訊。  使用左上角的時間控制項，即可查詢對應的過往時間範圍 (最長可達一小時)，以顯示相依性的過往情形，例如在事件發生期間或變更發生之前。    付費工作區的 ADM 資料會儲存 30 天，免費工作區的 ADM 資料則會儲存 7 天。

![已選取機器屬性的機器對應](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>失敗的連線
ADM 對應內會顯示處理程序和電腦的失敗連線，並以紅色虛線顯示用戶端系統是否無法連線到處理程序或連接埠。  已部署 ADM 代理程式的系統如果就是嘗試進行失敗連線的系統，則會報告失敗的連線。  ADM 會觀察無法建立連線的 TCP 通訊端來測量此失敗連線。  連線失敗可能是因為防火牆，用戶端或伺服器設定不正確，或遠端服務無法使用。 

![失敗的連線](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

了解失敗的連線有助於疑難排解、移轉驗證、安全性分析和整體架構理解。  失敗的連線有時無害，但它們通常會直指問題，例如容錯移轉環境突然變成無法連線，...或兩個應用程式層在雲端移轉之後無法通訊。  在上述影像中，IIS 和 WebSphere 都在執行，但無法連線。 

## <a name="computer-and-process-properties"></a>電腦和處理程序屬性
在瀏覽 ADM 對應時，您可以選取機器和處理程序來取得其他有關其屬性的內容。  機器會提供有關 DNS 名稱、IPv4 位址、CPU 和記憶體容量、VM 類型、作業系統版本、上次重新開機時間，以及其 OMS 和 ADM 代理程式識別碼的資訊。

處理程序的詳細資料是收集自作業系統中繼資料，其內容與執行中的處理程序有關，包括處理程序名稱、處理程序描述、使用者名稱和網域 (在 Windows 上)、公司名稱、產品名稱、產品版本、工作目錄、命令列，以及處理程序開始時間。

![處理程序屬性](media/operations-management-suite-application-dependency-monitor/process-properties.png)

「處理程序摘要」面板會提供其他有關該處理程序連線的資訊，包括其繫結連接埠、輸入及輸出連線，以及失敗的連線。 

![處理程序摘要](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>OMS 變更追蹤整合
當 ADM 和變更追蹤這兩個解決方案皆已在 OMS 工作區中啟用並設定，便會自動進行整合。

「機器摘要」面板會指出所選機器在所選時間範圍內是否有發生變更追蹤事件。

![機器摘要面板](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

「機器變更追蹤」面板會顯示所有變更的清單，從最新排到最舊，並有連結可供向下鑽研記錄檔搜尋，以取得其他詳細資料。
![機器變更追蹤面板](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

以下是在選取 [在 Log Analytics 中顯示] 後組態變更事件的向下鑽研檢視。
![組態變更事件](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)

## <a name="log-analytics-records"></a>Log Analytics 記錄
ADM 的電腦和處理程序清查資料可供在 Log Analytics 中進行[搜尋](../log-analytics/log-analytics-log-searches.md)。  這適用於包括移轉規劃、容量分析、探索和臨機操作效能疑難排解在內的案例。 

除了當處理程序或電腦啟動時或是新增到 ADM 時所產生的記錄外，每小時還會為每個唯一的電腦和處理程序產生一個記錄。  這些記錄具有下表中的屬性。 

有可用來識別唯一處理程序和電腦的內部產生屬性︰

* PersistentKey_s 會以處理程序組態 (例如命令列和使用者識別碼) 來進行唯一定義。  其為給定電腦所獨有，但可跨電腦重複。
* ProcessId_s 和 ComputerId_s 在 ADM 模型中則是全域唯一的。

### <a name="admcomputer_cl-records"></a>AdmComputer_CL 記錄
類型為 **AdmComputer_CL** 的記錄會有伺服器 (具有 ADM 代理程式) 的清查資料。  這些記錄具有下表中的屬性。  

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |AdmComputer_CL |
| SourceSystem |*OpsManager* |
| ComputerName_s |Windows 或 Linux 電腦名稱 |
| CPUSpeed_d |以 MHz 為單位的 CPU 速度 |
| DnsNames_s |此電腦的所有 DNS 名稱清單 |
| IPv4s_s |此電腦所使用的所有 IPv4 位址清單 |
| IPv6s_s |此電腦所使用的所有 IPv6 位址清單   (ADM 可識別 IPv6 位址，但不會探索 IPv6 相依性)。 |
| Is64Bit_b |true 或 false (根據 OS 類型) |
| MachineId_s |跨 OMS 工作區唯一的內部 GUID |
| OperatingSystemFamily_s |Windows 或 Linux |
| OperatingSystemVersion_s |完整 OS 版本字串 |
| TimeGenerated |記錄的建立日期和時間。 |
| TotalCPUs_d |CPU 核心數目 |
| TotalPhysicalMemory_d |記憶體容量 (MB) |
| VirtualMachine_b |true 或 false (根據 OS 是否為 VM 客體) |
| VirtualMachineID_g |Hyper-V VM 識別碼 |
| VirtualMachineName_g |Hyper-V VM 名稱 |
| VirtualMachineType_s |Hyperv、Vmware、Xen、Kvm、Ldom、Lpar、Virtualpc |

### <a name="admprocess_cl-type-records"></a>AdmProcess_CL 類型記錄
類型為 **AdmProcess_CL** 的記錄會有伺服器 (具有 ADM 代理程式) 上 TCP 連線處理程序的清查資料。  這些記錄具有下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |AdmProcess_CL |
| SourceSystem |*OpsManager* |
| CommandLine_s |處理程序的完整命令列 |
| CompanyName_s |公司名稱 (來自 Windows PE 或 Linux RPM) |
| Description_s |完整處理程序描述 (來自 Windows PE 或 Linux RPM) |
| FileVersion_s |可執行檔版本 (僅限來自 Windows PE、Windows) |
| FirstPid_d |OS 處理程序識別碼 |
| InternalName_s |可執行檔的內部名稱 (僅限來自 Windows PE、Windows) |
| MachineId_s |跨 OMS 工作區唯一的內部 GUID |
| Name_s |處理程序可執行檔名稱 |
| Path_s |處理程序可執行檔的檔案系統路徑 |
| PersistentKey_s |在這台電腦中是唯一的內部 GUID |
| PoolId_d |用來根據類似命令列彙總處理程序的內部識別碼。 |
| ProcessId_s |跨 OMS 工作區唯一的內部 GUID |
| ProductName_s |產品名稱字串 (來自 Windows PE 或 Linux RPM) |
| ProductVersion_s |產品版本字串 (來自 Windows PE 或 Linux RPM) |
| StartTime_t |本機電腦時鐘上的處理程序開始時間 |
| TimeGenerated |記錄的建立日期和時間。 |
| UserDomain_s |處理程序擁有者的網域 (僅限 Windows) |
| UserName_s |處理程序擁有者的名稱 (僅限 Windows) |
| WorkingDirectory_s |處理程序工作目錄 |

## <a name="sample-log-searches"></a>記錄搜尋範例
### <a name="list-the-physical-memory-capacity-of-all-managed-computers."></a>列出所有受管理電腦的實體記憶體容量。
Type=AdmComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name,-dns,-ip,-and-os-version."></a>列出電腦名稱、DNS、IP 和 OS 版本。
Type=AdmComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s | dedup ComputerName_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-"sql"-in-the-command-line"></a>在命令列中尋找具有「sql」的所有處理程序
Type=AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process,-use-its-machine-id-to-retrieve-the-computer’s-name"></a>在檢視給定處理程序的事件資料之後，使用其機器識別碼來擷取電腦的名稱
Type=AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>列出所有執行 SQL 的電腦
Type=AdmComputer_CL MachineId_s IN {Type=AdmProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>資料中心內所有唯一 curl 產品版本的清單
Type=AdmProcess_CL Name_s=curl | Distinct ProductVersion_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>為所有執行 CentOS 的電腦建立電腦群組
![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)

## <a name="diagnostic-and-usage-data"></a>診斷和使用量資料
當您使用應用程式相依性監視服務時，Microsoft 會自動收集使用量和效能資料。 Microsoft 使用這項資料來提供和改進應用程式相依性監視服務的品質、安全性和完整性。 資料中除了包含軟體 (如作業系統) 組態和版本的相關資訊，還包含 IP 位址、DNS 名稱和工作站名稱，以便提供精確且有效率的疑難排解功能。 我們不會收集姓名、地址或其他連絡資訊。

如需有關資料收集與使用方式的詳細資訊，請參閱 [Microsoft 線上服務隱私權聲明](hhttps://go.microsoft.com/fwlink/?LinkId=512132)。

## <a name="next-steps"></a>後續步驟
* 深入了解[記錄檔搜尋](../log-analytics/log-analytics-log-searches.md\] in Log Analytics to retrieve data collected by Application Dependency Monitor..md)

<!--HONumber=Oct16_HO2-->


