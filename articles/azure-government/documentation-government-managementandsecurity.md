<properties
    pageTitle="Azure Government 文件 | Microsoft Azure"
    description="這為 Azure Government 的開發應用程式提供功能和指引的比較"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/05/2016"
    ms.author="scooxl"/>

#  <a name="azure-government-management-and-security"></a>Azure Government 管理和安全性
##  <a name="key-vault"></a>金鑰保存庫
如需這項服務和使用方式的詳細資料，請參閱 <a href="https://azure.microsoft.com/documentation/services/key-vault">Azure 金鑰保存庫公開文件</a>。
### <a name="data-considerations"></a>資料考量
下列資訊可識別 Azure 金鑰保存庫的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 使用 Azure 金鑰保存庫金鑰來加密的所有資料都可包含受管制/受控制資料。 | Azure 金鑰保存庫中繼資料不可以包含受到匯出管制的資料。 此中繼資料包括建立和維護金鑰保存庫時所輸入的所有組態資料。  請勿輸入受管制/受控制資料到下列欄位︰資源群組名稱、金鑰保存庫名稱、訂用帳戶名稱 |

金鑰保存庫已在 Azure Government 中正式推出。 和公開版一樣，其中沒有擴充功能，所以只可透過 PowerShell 和 CLI 取得金鑰保存庫。
## <a name="log-analytics"></a>Log Analytics
Log Analytics 已在 Azure Government 中正式推出。 

### <a name="differences-from-public-azure"></a>與公用 Azure 不同之處

下列 Log Analytics 功能和解決方案目前無法在 Azure Government 中使用。 功能/解決方案的狀態變更時，這份清單會隨之更新。

+ 在公用 Azure 中預覽的解決方案包括︰
  - 網路監視解決方案
  - Azure 網路分析解決方案
  - Office 365 解決方案
  - Windows 10 Upgrade Analytics 解決方案
  - 應用程式相依性監視
  - Application Insights
  - Azure 活動記錄檔
  - Azure 自動化分析
  - 金鑰保存庫分析
+ 需要 Azure 自動化的解決方案和功能包括︰
  - 更新管理
  - 變更管理
  - 觸發 Azure 自動化 Runbook 的警示
+ 需要更新內部部署軟體的解決方案和功能包括
  - 與 System Center Operations Manager 2016 的整合
  - 來自 System Center Configuration Manager 的電腦群組
  - Surface Hub 解決方案
+ 在公用 Azure 中預覽的功能包括︰
  - 將資料匯出至 PowerBI
+ Azure 入口網站整合
  - 必須透過 PowerShell 或 Resource Manager 範本來選取要監視的 Azure 儲存體帳戶
  - 必須透過 PowerShell 或 Resource Manager 範本來選取虛擬機器以啟用 Log Analytics 代理程式
  - Azure 度量和 Azure 診斷
+ OMS 行動應用程式
+ OMS Linux 代理程式 VM 擴充功能
+ 使用狀況資料

下列 Log Analytics 功能在 Azure Government 中會有不同的行為：

+ 必須從 Azure Government 的 [Log Analytics 入口網站](https://oms.microsoft.us)下載 Windows 代理程式。
+ 使用資料收集器 API 上傳資料需要使用 Azure Government 的 URL (https://*workspaceId*.ods.opinsights.azure.us，其中 *workspaceId* 是來自 OMS 入口網站的工作區識別碼)。 
+ 若要將 System Center Operations Manager 管理伺服器連接到 Log Analytics，您需要下載及匯入更新的管理組件。
  1. 下載及儲存[更新的管理組件](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. 將下載的檔案解壓縮
  3. 將管理組件匯入 Operations Manager。 如需從磁碟匯入管理組件的相關資訊，請參閱 Microsoft TechNet 網站上的[如何匯入 Operations Manager 管理組件](http://technet.microsoft.com/library/hh212691.aspx)主題。
  4. 若要將 Operations Manager 連接到 Log Analytics，請遵循[將 Operations Manager 連接到 Log Analytics](../log-analytics/log-analytics-om-agents.md) 中的步驟 



### <a name="frequently-asked-questions"></a>常見問題集

+ 可以從公用 Azure 中的 Log Analytics 移轉資料至 Azure Government 嗎？
  - 不可以。 不能將資料或您的工作區從公用 Azure 移動至 Azure Government。
+ 可以從 OMS Log Analytics 入口網站切換公用 Azure 和 Azure Government 工作區嗎？
  - 不可以。 公用 Azure 和 Azure Government 的入口網站是分開的，而且不共用資訊。 

如需詳細資訊，請參閱 [Log Analytics 公開文件](../log-analytics/log-analytics-overview.md)。

## <a name="next-steps"></a>後續步驟

如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>
 



<!--HONumber=Oct16_HO2-->


