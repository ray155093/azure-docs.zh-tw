---
title: "Azure Government 監視和管理 | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較。"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/31/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 3e460dd2aaf51ef2ba9840513d236ce313320c80
ms.openlocfilehash: 39a87bdbef7c532f9e50f1e71e9c12797bc9a33c


---
# <a name="azure-government-monitoring--management"></a>Azure Government 監視和管理
本文概述 Azure Government 環境的監視和管理服務變化以及考量。

## <a name="automation"></a>自動化
自動化已在 Azure Government 中正式推出。

### <a name="variations"></a>變化
下列自動化功能目前無法在 Azure Government 中使用。

* 建立服務主體認證進行驗證

如需詳細資訊，請參閱[自動化公開文件](../automation/automation-intro.md)。

## <a name="backup"></a>備份
備份已在 Azure Government 中正式推出。

如需詳細資訊，請參閱 [Azure Government 備份](documentation-government-services-backup.md)。

### <a name="variations"></a>變化
下列備份功能目前無法在 Azure Government 中使用：

* Azure Resource Manager 保存庫
* 使用 Azure 入口網站進行管理 (支援 Azure 傳統入口網站)

Azure Government 中的備份 URL 不同：

## <a name="site-recovery"></a>Site Recovery
Site Recovery (ASR) 已在 Azure Government 中正式推出。

如需詳細資訊，請參閱 [Site Recovery 公開文件](../site-recovery/site-recovery-overview.md)。

### <a name="variations"></a>變化
下列 Site Recovery 功能目前無法在 Azure Government 中使用：

* Azure Resource Manager Site Recovery 保存庫

| Site Recovery | 傳統 | 資源管理員 | 
| --- | --- | --- |
| VMWare/實體  | GA | 已規劃 |
| Hyper-V | GA | 已規劃 |
| 網站對網站 | GA | 已規劃 |

注意：資料表適用於 VA 和 Iowa。 

下列 ASR URL 在 Azure Government 中不同：

| Azure 公用 | Azure Government | 注意事項 |
| --- | --- | --- |
| *.hypervrecoverymanager.windowsazure.com | *.hypervrecoverymanager.windowsazure.us | Site Recovery 服務的存取權 |
| *. backup.windowsazure.com  | *.backup.windowsazure.us | 保護服務的存取權 |
| *.blob.core.windows.net | *.blob.core.usgovcloudapi.net | 用於儲存 VM 快照集 |
| http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | 下載 MySQL |



## <a name="log-analytics"></a>Log Analytics
Log Analytics 已在 Azure Government 中正式推出。

### <a name="variations"></a>變化
下列 Log Analytics 功能和解決方案目前無法在 Azure Government 中使用。

* 在 Microsoft Azure 中預覽的解決方案包括︰
  * 網路監視解決方案
  * 應用程式相依性監視解決方案
  * Office 365 解決方案
  * Windows 10 Upgrade Analytics 解決方案
  * Application Insights 解決方案
  * Azure 網路分析解決方案
  * Azure 自動化分析解決方案
  * Key Vault 分析解決方案
* 需要更新內部部署軟體的解決方案和功能包括：
  * 與 System Center Operations Manager 2016 的整合 (支援舊版 Operations Manager)
  * 來自 System Center Configuration Manager 的電腦群組
  * Surface Hub 解決方案
* 在公用 Azure 中預覽的功能包括︰
  * 將資料匯出至 Power BI
* Azure 度量和 Azure 診斷
* Operations Management Suite 行動應用程式

Azure Government 中的 Log Analytics URL 不同：

| Azure 公用 | Azure Government | 注意事項 |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics 入口網站 |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[資料收集器 API](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |代理程式通訊 - [設定防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |代理程式通訊 - [設定防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |代理程式通訊 - [設定防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |

下列 Log Analytics 功能在 Azure Government 中會有不同的行為：

* 必須從 Azure Government 的 [Log Analytics 入口網站](https://oms.microsoft.us)下載 Windows 代理程式。
* 若要將 System Center Operations Manager 管理伺服器連接到 Log Analytics，您需要下載及匯入更新的管理組件。
  1. 下載及儲存[更新的管理組件](http://go.microsoft.com/fwlink/?LinkId=828749)。
  2. 將下載的檔案解壓縮。
  3. 將管理組件匯入 Operations Manager。 如需如何從磁碟匯入管理組件的相關資訊，請參閱 Microsoft TechNet 網站上的[如何匯入 Operations Manager 管理組件](http://technet.microsoft.com/library/hh212691.aspx)。
  4. 若要將 Operations Manager 連接到 Log Analytics，請遵循[將 Operations Manager 連接到 Log Analytics](../log-analytics/log-analytics-om-agents.md) 中的步驟。

### <a name="frequently-asked-questions"></a>常見問題集
* 可以將資料從 Microsoft Azure 的 Log Analytics 中移轉至 Azure Government 嗎？
  * 否。 不能將資料或您的工作區從 Microsoft Azure 移動至 Azure Government。
* 可以從 Operations Management Suite Log Analytics 入口網站切換 Microsoft Azure 與 Azure Government 工作區嗎？
  * 否。 Microsoft Azure 和 Azure Government 的入口網站是分開的，而且不共用資訊。

如需詳細資訊，請參閱 [Log Analytics 公開文件](../log-analytics/log-analytics-overview.md)。

## <a name="site-recovery"></a>Site Recovery
Site Recovery 已在 Azure Government 中正式推出。

如需詳細資訊，請參閱 [Site Recovery 公開文件](../site-recovery/site-recovery-overview.md)。

### <a name="variations"></a>變化
下列 Site Recovery 功能目前無法在 Azure Government 中使用：

* Azure Resource Manager Site Recovery 保存庫

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 部落格。 </a>



<!--HONumber=Dec16_HO2-->


