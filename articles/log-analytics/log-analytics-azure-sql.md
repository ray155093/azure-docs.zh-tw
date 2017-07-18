---
title: "Log Analytics 中的 Azure SQL Analytics 解決方案 | Microsoft Docs"
description: "Azure SQL Analytics 解決方案可協助您管理 Azure SQL Database。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: f5f9aa186480926df1110928983566e05f79efb8
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>使用 Azure SQL Database (預覽) 監視 Log Analytics 中的 Azure SQL Database

![Azure SQL 分析符號](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure Log Analytics 中的 Azure SQL 分析解決方案會收集並以視覺化方式檢視重要的 SQL Azure 效能計量。 藉由使用您以解決方案收集的計量，您可以建立自訂的監視規則和警示。 而且，您可以跨多個 Azure 訂用帳戶和彈性集區監視 Azure SQL Database 和彈性集區計量，並將其視覺化。 解決方案也可協助您找出應用程式堆疊中每個層級的問題。  它會使用 [Azure 診斷計量](log-analytics-azure-storage.md)與 Log Analytics 檢視來呈現單一 Log Analytics 工作區中所有 Azure SQL Database 和彈性集區的相關資料。

目前，此預覽解決方案針對每個工作區支援高達 150,000 個 Azure SQL Database 和 5,000 個 SQL 彈性集區。

Azure SQL 分析解決方案，如同其他可用的 Log Analytics，可協助您監視並接收您 Azure 資源健康情況的相關通知，在此情況下為 Azure SQL Database。 Microsoft Azure SQL Database 是可調整的關聯式資料庫服務，可對 Azure 雲端中執行的應用程式提供熟悉的 SQL Server 類似功能。 Log Analytics 可協助您收集、相互關聯，並以視覺化方式檢視結構化和非結構化資料。

## <a name="connected-sources"></a>連接的來源

Azure SQL 分析解決方案不使用代理程式連線至 Log Analytics 服務。

下表描述此方案支援的連接來源。

| 連接的來源 | 支援 | 說明 |
| --- | --- | --- |
| [Windows 代理程式](log-analytics-windows-agents.md) | 否 | 解決方案不使用直接 Windows 代理程式。 |
| [Linux 代理程式](log-analytics-linux-agents.md) | 否 | 解決方案不使用直接 Linux 代理程式。 |
| [SCOM 管理群組](log-analytics-om-agents.md) | 否 | 解決方案不使用從 SCOM 代理程式直接連線到 Log Analytics。 |
| [Azure 儲存體帳戶](log-analytics-azure-storage.md) | 否 | Log Analytics 不會從儲存體帳戶讀取資料。 |
| [Azure 診斷](log-analytics-azure-storage.md) | 是 | Azure 會將 Azure 計量資料直接傳送至 Log Analytics。 |

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有帳戶，您可以[免費](https://azure.microsoft.com/free/)建立一個。
- Log Analytics 工作區。 您可以使用現有的帳戶，或者您可以在開始使用此解決方案之前[建立一個新的](log-analytics-get-started.md)。
- 針對您的 Azure SQL Database 和彈性集區啟用 Azure 診斷，並[將其設定為傳送資料至 Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)。

## <a name="configuration"></a>組態

執行下列步驟將 Azure SQL 分析解決方案新增至您的工作區。

1. 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) 或使用[從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)中所述的程序，將 Azure SQL Analytics 解決方案新增至您的工作區。
2. 在 Azure 入口網站中，按一下 [新增] \(+ 符號)，然後在資源的清單中，選取 [監視 + 管理]。  
    ![監視 + 管理](./media/log-analytics-azure-sql/monitoring-management.png)
3. 在 [監視 + 管理] 清單中，按一下 [檢視全部]。
4. 在 [建議]清單中，按一下 [詳細]，然後在新的清單中，尋找 **Azure SQL 分析 (預覽)**，然後選取它。  
    ![Azure SQL 分析解決方案](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. 在 **Azure SQL 分析 (預覽)** 刀鋒視窗中，按一下 [建立]。  
    ![建立](./media/log-analytics-azure-sql/portal-create.png)
6. 在 [建立新方案] 刀鋒視窗中，選取您想要新增解決方案的工作區，然後按一下 [建立]。  
    ![新增到工作區](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>若要設定多個 Azure 訂用帳戶

若要支援多個訂用帳戶，從[使用 PowerShell 啟用 Azure 資源計量記錄](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)使用 PowerShell 指令碼。 當執行指令碼以將診斷資料從一個 Azure 訂用帳戶中的資源傳送至另一個 Azure 訂用帳戶中的工作區時，提供工作區資源識別碼做為參數。

**範例**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>使用解決方案

當您將解決方案新增至您的工作區時，Azure SQL 分析圖格會新增至您的工作區，而且會顯示在 [概觀] 中。 圖格會顯示 Azure SQL Database 和解決方案所連接之 Azure SQL 彈性集區的數目。

![Azure SQL 分析圖格](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>檢視 Azure SQL 分析資料

按一下 [Azure SQL 分析] 圖格以開啟 Azure SQL 分析儀表板。 此儀表板包含下面定義的刀鋒視窗。 每個刀鋒視窗最多可列出 15 個資源 (訂用帳戶、伺服器、彈性集區及資料庫)。 按一下任何資源，以開啟該特定資源的儀表板。 彈性集區或資料庫包含的圖表具有所選資源的計量。 按一下圖表，以開啟 [記錄搜尋] 對話方塊。

| 刀鋒視窗 | 說明 |
|---|---|
| 訂用帳戶 | 具有已連線伺服器、集區及資料庫數目的訂用帳戶清單。 |
| 伺服器 | 具有已連線集區和資料庫數目的伺服器清單。 |
| 彈性集區 | 具有觀察期間內最大 GB 和 eDTU 的已連線彈性集區清單。 |
|資料庫 | 具有觀察期間內最大 GB 和 DTU 的已連線資料庫清單。|


### <a name="analyze-data-and-create-alerts"></a>分析資料並建立警示

這個方案包含有用的查詢，可協助分析您的資料。 如果您向右捲動，儀表板會列出數個常見的查詢，按一下即可執行 Azure SQL 資料的[記錄搜尋](log-analytics-log-searches.md)。

![查詢](./media/log-analytics-azure-sql/azure-sql-queries.png)

解決方案包括一些警示型查詢，如上所示，可供您用於 Azure SQL Database 與彈性集區的特定臨界值警示。

#### <a name="to-configure-an-alert-for-your-workspace"></a>若要設定您工作區的警示

1. 前往 [OMS 入口網站](http://mms.microsoft.com/)並登入。
2. 開啟您針對解決方案所設定之工作區。
3. 在 [概觀] 頁面上，按一下 [Azure SQL 分析 (預覽)] 圖格。
4. 向右捲動然後按一下查詢，以開始建立警示。  
![警示查詢](./media/log-analytics-azure-sql/alert-query.png)
5. 在記錄搜尋中，按一下 [警示]。  
![在搜尋中建立警示](./media/log-analytics-azure-sql/create-alert01.png)
6. 在 [新增警示規則] 頁面上，設定您要的適當屬性和特定臨界值，然後按一下 [儲存]。  
![新增警示規則](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-analytics-data"></a>對 Azure SQL 分析資料採取行動

做為範例，您可以執行的其中一個最有用查詢，是跨所有 Azure 訂用帳戶比較所有 Azure SQL 彈性集區的 DTU 使用量。 資料庫輸送量單位 (DTU) 提供說明 Basic、Standard 和 Premium 資料庫與集區的效能層級相對容量。 DTU 是根據 CPU、記憶體、讀與寫的混合測量。 當 DTU 增加時，會增加效能層級所提供的強大功能。 例如，具有 5 個 DTU 的效能層級比具有 1 個 DTU 的效能層級有五倍的能力。 最大 DTU 配額適用於每個伺服器和彈性集區。

藉由執行下列記錄搜尋查詢，您可以輕易地分辨是否未充分使用或過度使用您的 SQL Azure 彈性集區。

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

在下列範例中，您可以看到一個彈性集區有接近 100 % DTU 的高使用量，而有些則有極少的使用量。 您可以進一步調查以在您環境中使用 Azure 活動記錄疑難排解潛在的最新變更。

![記錄搜尋結果 - 高使用率](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>另請參閱

- 使用 Log Analytics 中的[記錄搜尋](log-analytics-log-searches.md)來檢視詳細的 Azure SQL 資料。
- [建立您自己的儀表板](log-analytics-dashboards.md)來顯示 Azure SQL 資料。
- 在特定的 Azure SQL 事件發生時[建立警示](log-analytics-alerts.md)。

