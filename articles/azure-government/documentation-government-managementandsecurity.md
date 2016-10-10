<properties
	pageTitle="Azure Government 文件 | Microsoft Azure"
	description="這為 Azure Government 的開發應用程式提供功能和指引的比較"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="09/23/2016"
	ms.author="ryansoc"/>


#  Azure Government 管理和安全性

##  金鑰保存庫

下列資訊可識別 Azure 金鑰保存庫的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 使用 Azure 金鑰保存庫金鑰來加密的所有資料都可包含受管制/受控制資料。 | Azure 金鑰保存庫中繼資料不可以包含受到匯出管制的資料。此中繼資料包括建立和維護金鑰保存庫時所輸入的所有組態資料。請勿輸入受管制/受控制資料到下列欄位︰資源群組名稱、金鑰保存庫名稱、訂用帳戶名稱 |

金鑰保存庫已在 Azure Government 中正式推出。和公開版一樣，其中沒有擴充功能，所以只可透過 PowerShell 和 CLI 取得金鑰保存庫。

如需詳細資訊，請參閱 [Azure 金鑰保存庫公開文件](/key-vault-get-started)。

## Log Analytics

Log Analytics 在 Azure Government 中是預覽功能。

### 與公用 Azure 不同之處

下列 Log Analytics 功能和解決方案目前無法在 Azure Government 中使用。功能/解決方案的狀態變更時，這份清單會隨之更新。

+ 近乎即時進行的計量
  - 當您檢視時間範圍少於六小時的計量圖時，圖形不會自動更新為新的計量值
+ 將資料匯出至 PowerBI
+ 網路監視解決方案
+ Office 365 解決方案
+ Windows 10 Upgrade Analytics 解決方案
+ 應用程式相依性監視
+ 更新評估
+ Azure 入口網站整合
  - 必須透過 PowerShell 或 Resource Manager 範本來選取要監視的 Azure 儲存體帳戶
  - 必須透過 PowerShell 或 Resource Manager 範本來選取虛擬機器以啟用 Log Analytics 代理程式
+ Linux 監視
+ OMS 行動應用程式
+ Microsoft Monitoring Agent VM 擴充功能
+ OMS Linux 代理程式 VM 擴充功能
+ 使用狀況資料
+ 使用 Azure 自動化的警示電子郵件和補救措施

下列 Log Analytics 功能在 Azure Government 中會有不同的行為：

+ 必須從 Azure Government 的 Log Analytics 入口網站下載 Windows 代理程式。
+ 「安全性和稽核」解決方案並不支援惡意 IP 偵測。
+ 使用資料收集器 API 上傳資料需要使用 Azure Government 的 URL。

### 常見問題集

+ 可以從公用 Azure 中的 Log Analytics 移轉資料至 Azure Government 嗎？
  - 不可以。不能將資料或您的工作區從公用 Azure 移動至 Azure Government。
+ 可以從 OMS Log Analytics 入口網站切換公用 Azure 和 Azure Government 工作區嗎？
  - 不可以。公用 Azure 和 Azure Government 的入口網站是分開的，而且不共用資訊。

如需詳細資訊，請參閱 [Log Analytics 公開文件](../log-analytics/log-analytics-overview.md)。

## 後續步驟

如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。</a>

<!---HONumber=AcomDC_0928_2016-->