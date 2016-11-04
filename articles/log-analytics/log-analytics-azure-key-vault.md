---
title: Log Analytics 中的 Azure 金鑰保存庫解決方案 | Microsoft Docs
description: 您可以使用 Log Analytics 中的 Azure 金鑰保存庫解決方案來檢閱 Azure 金鑰保存庫記錄檔。
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: richrund

---
# <a name="azure-key-vault-(preview)-solution-in-log-analytics"></a>Log Analytics 中的 Azure 金鑰保存庫 (預覽) 解決方案
> [!NOTE]
> 這是[預覽解決方案](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)。
> 
> 

您可以使用 Log Analytics 中的 Azure 金鑰保存庫解決方案來檢閱 Azure 金鑰保存庫 AuditEvent 記錄檔。

您可以對 Azure 金鑰保存庫的稽核事件啟用記錄。 這些記錄會寫入至 Azure Blob 儲存體，經過 Log Analytics 編製索引後，即可供搜尋和分析。

## <a name="install-and-configure-the-solution"></a>安裝和設定解決方案
使用下列指示來安裝和設定 Azure 金鑰保存庫解決方案︰

1. 對您想要監視的[金鑰保存庫資源啟用診斷記錄](../key-vault/key-vault-logging.md)
2. 使用 [Blob 儲存體中的 JSON 檔案](log-analytics-azure-storage-json.md)中所述的程序，設定 Log Analytics 從 Blob 儲存體讀取記錄。
3. 使用[從方案庫新增 Log Analytics 解決方案](log-analytics-add-solutions.md)中所述的程序，啟用 Azure 金鑰保存庫解決方案。  

## <a name="review-azure-key-vault-data-collection-details"></a>檢閱 Azure 金鑰保存庫資料收集的詳細資料
Azure 金鑰保存庫解決方案會從 Azure Blob 儲存體收集 Azure 金鑰保存庫的診斷記錄。
資料收集不需要任何代理程式。

下表顯示 Azure 金鑰保存庫的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | Systems Center Operations Manager (SCOM) 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![否](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![否](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![是](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![否](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![否](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |10 分鐘 |

## <a name="use-azure-key-vault"></a>使用 Azure 金鑰保存庫
安裝此解決方案之後，您可以在 Log Analytics 的 [概觀] 頁面上使用 [Azure 金鑰保存庫] 圖格，檢視受監視的金鑰保存庫經過一段時間之後要求狀態的摘要。

![Azure 金鑰保存庫圖格的影像](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

按一下 [概觀] 圖格之後，您可以檢視記錄檔的摘要，然後深入下列類別的詳細資訊︰

* 經過一段時間的所有金鑰保存庫作業的數量
* 經過一段時間的失敗作業數量
* 依作業的平均作業延遲
* 作業的服務品質，顯示花費超過 1000 毫秒的作業數目及花費超過 1000 毫秒的作業清單

![Azure 金鑰保存庫儀表板的影像](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Azure 金鑰保存庫儀表板的影像](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>檢視任何作業的詳細資料
1. 在 [概觀] 頁面上，按一下 [Azure 金鑰保存庫] 圖格。
2. 在 [Azure 金鑰保存庫] 儀表板上，檢閱其中一個刀鋒視窗中的摘要資訊，然後按一下其中一個，在記錄搜尋頁面中檢視詳細資訊。
   
    您可以在任何 [記錄搜尋] 頁面上，按時間、詳細結果和您的記錄搜尋記錄來檢視結果。 您也可以按 Facet 篩選以縮減結果。

## <a name="log-analytics-records"></a>Log Analytics 記錄
Azure 金鑰保存庫解決方案會分析從 Azure 診斷的 [AuditEvent 記錄檔](../key-vault/key-vault-logging.md)收集的 **KeyVaults** 類型記錄。  下表是這些記錄的屬性。  

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*KeyVaults* |
| SourceSystem |*AzureStorage* |
| CallerIpAddress |提出要求之用戶端的 IP 位址 |
| 類別 |對於金鑰保存庫記錄來說，AuditEvent 是其唯一可用的值。 |
| CorrelationId |選擇性的 GUID，用戶端可傳遞此 GUID 來讓用戶端記錄與服務端 (金鑰保存庫) 記錄相互關聯。 |
| DurationMs |服務 REST API 要求時所花費的時間，以毫秒為單位。 這不包括網路延遲，因此在用戶端所測量到的時間可能不符合此時間。 |
| HttpStatusCode_d |由要求傳回的 HTTP 狀態碼 |
| Id_s |要求的唯一識別碼 |
| Identity_o |在提出 REST API 要求時所提供之權杖中的身分識別。 就像從 Azure PowerShell Cmdlet 產生的要求一樣，這通常是「使用者」、「服務主體」或「使用者+appId」的組合。 |
| OperationName |[Azure 金鑰保存庫記錄](../key-vault/key-vault-logging.md)中所記載的作業名稱 |
| OperationVersion |用戶端所要求的 REST API 版本 |
| RemoteIPLatitude |提出要求之用戶端的緯度 |
| RemoteIPLongitude |提出要求之用戶端的經度 |
| RemoteIPCountry |提出要求之用戶端的國家 |
| RequestUri_s |要求的 Uri |
| 資源 |金鑰保存庫的名稱 |
| ResourceGroup |金鑰保存庫的資源群組 |
| ResourceId |Azure 資源管理員資源識別碼。 對於金鑰保存庫記錄來說，這一律是金鑰保存庫的資源識別碼。 |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResultSignature |HTTP 狀態 |
| ResultType |REST API 要求的結果 |
| SubscriptionId |包含金鑰保存庫的訂用帳戶的 Azure 訂用帳戶識別碼 |

## <a name="next-steps"></a>後續步驟
* 使用 [Log Analytics 中的記錄搜尋](log-analytics-log-searches.md)來檢視詳細的 Azure 金鑰保存庫資料。

<!--HONumber=Oct16_HO2-->


