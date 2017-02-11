---
title: "Azure Government Web、行動和 API | Microsoft Docs"
description: "這為 Azure Government 的開發應用程式提供功能和指引的比較"
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/5/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: f0ba3e50c4281a0e8c365be075ae45ab72f6e82d

---
# <a name="azure-government-web--mobile"></a>Azure Government Web + 行動
## <a name="app-services"></a>應用程式服務
### <a name="variations"></a>變化
Azure App Service 已在 Azure Government 中正式推出。

在 Azure Government 中所建立之 Azure App Service 應用程式的位址，不同於在公用雲端中所建立的位址︰

| 服務類型 | Azure 公用 | Azure Government |
| --- | --- | --- |
| App Service |*.azurewebsites.net |*.azurewebsites.us|

某些在公用雲端中可用的 App Service 功能，尚無法在 Azure Government 中使用︰

- App Service 環境
- 應用程式部署
    - 持續傳遞 (預覽)
- 設定
    - vNet 整合式和混合式連線
    - 安全性掃描
- 開發工具
    - 效能測試
    - 資源總管
    - PHP 偵錯
- 監視
    - Application Insights
    - 每個執行個體的度量
    - 即時 HTTP 流量
    - 應用程式事件
    - FREB 記錄檔
- 支援 + 疑難排解
    - App Service 建議程式
    - 失敗歷程記錄
    - 診斷即服務
    - 減輕問題


### <a name="considerations"></a>考量
下列資訊可識別 App Service 的 Azure Government 界限︰

| 允許受管制/受控制資料 | 不允許受管制/受控制資料 |
| --- | --- |
| 在 Azure App Service 內輸入、儲存及處理的資料可以包含受到匯出管制的資料。 二進位檔案是在 Azure App Service 內執行。 靜態驗證器，例如用來存取 Azure 平台元件的密碼和智慧卡 PIN。 用來管理 Azure 平台元件之憑證的私密金鑰。 SQL 連接字串。 其他安全性資訊/機密資料，例如儲存在 Azure 服務的憑證、加密金鑰、主要金鑰和儲存體金鑰。 |中繼資料不可以包含受到匯出管制的資料。 此中繼資料包括建立和維護 Azure App Service 時所輸入的所有組態資料。 請不要將受管制/受控制資料輸入至下列欄位︰資源群組、資源名稱、資源標籤|

## <a name="next-steps"></a>後續步驟
如需補充資訊和更新，請訂閱 [Microsoft Azure Government 部落格](https://blogs.msdn.microsoft.com/azuregov/)。




<!--HONumber=Dec16_HO2-->


