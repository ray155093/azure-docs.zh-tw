<properties
	pageTitle="監視 DocumentDB 要求、使用量及儲存體 | Microsoft Azure"
	description="了解如何監視 DocumentDB 帳戶的效能度量 (如要求和伺服器錯誤) 和使用量度量 (如儲存體耗用量)。"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="mimig"/>

# 監視 DocumentDB 要求、使用量及儲存體

您可以在 [Azure 入口網站](https://portal.azure.com/)中監視 Azure DocumentDB 帳戶。每一個 DocumentDB 帳戶都有效能度量 (例如要求和伺服器錯誤) 和使用量度量 (例如儲存體耗用量) 可供使用。

## 在入口網站中檢視效能度量 
1.	在新視窗中，開啟 [Azure 入口網站](https://portal.azure.com/)，依序按一下 [更多服務] 和 [DocumentDB (NoSQL)]，然後按一下您想要檢視效能度量的 DocumentDB 帳戶名稱。
2.	[監視] 功能濾鏡預設會顯示以下圖格：
	*	當日的要求總數。
	*	已使用儲存體。

	如果您的表格顯示 [沒有可用的資料]，但您認為資料庫中有資料，請參閱[疑難排解](#troubleshooting)一節。

	![[監視] 功能濾鏡的螢幕擷取畫面，可顯示要求和儲存體使用量](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.	按一下 [要求] 或 [儲存體] 圖格，即會開啟詳細的 [度量] 刀鋒視窗。
4.	[**度量**] 刀鋒視窗會顯示您已選取之度量的詳細資料。刀鋒視窗上方是每小時繪製為圖表的要求圖形，而下方表格會顯示已節流處理的要求和要求總數的彙總值。度量刀鋒視窗也會顯示已定義的警示清單，且依目前度量刀鋒視窗上出現的度量來篩選 (因此，如果您有許多警示，只會看到此處顯示相關的警示)。

	![[度量] 刀鋒視窗的螢幕擷取畫面，其中包已節流處理的要求](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## 在入口網站中自訂效能度量檢視

1.	若要自訂可在特定圖表中顯示的度量，按一下圖表以在 [度量] 刀鋒視窗中開啟該圖表，然後按一下 [編輯圖表]。![[度量] 刀鋒視窗控制項的螢幕擷取畫面，其中已將 [編輯圖表] 反白顯示](./media/documentdb-monitor-accounts/madocdb3.png)

2.	在 [編輯圖表] 刀鋒視窗上，會提供選項來修改圖表中顯示的度量及其時間範圍。![[編輯圖表] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb4.png)

3.	若要變更組件中顯示的度量，只需選取或清除可用的效能度量，然後按一下刀鋒視窗底部的 [確定]。
4.	若要變更時間範圍，選擇不同的範圍 (例如 [自訂])，然後按一下刀鋒視窗底部的 [確定]。

	![[編輯圖表] 刀鋒視窗的 [時間範圍] 部分螢幕擷取畫面，顯示如何輸入自訂時間範圍](./media/documentdb-monitor-accounts/madocdb5.png)


## 在入口網站中建立並排圖表
Azure 入口網站可讓您建立並排度量圖表。

1.	首先，以滑鼠右鍵按一下要複製的圖表，然後選取 [自訂]。

	![包含反白顯示 [自訂] 選項的 [要求總數] 圖表螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb6.png)

2.	在功能表上按一下 [**複製**] 以複製組件，然後按一下 [**完成自訂**]。

	![包含反白顯示 [複製] 與 [完成] 自訂選項的 [要求總數] 圖表螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb7.png)


現在，您可以將此組件視為其他任何度量組件，並自訂該組件中顯示的度量和時間範圍。如此一來，您可以同時看到兩個不同的度量圖表並排出現。![[要求總數] 圖表和全新 [要求總數] 前一個小時圖表的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb8.png)

## 在入口網站中設定警示
1.	在 [Azure 入口網站](https://portal.azure.com/)中，依序按一下 [更多服務] 和 [DocumentDB (NoSQL)]，然後按一下您想要設定效能度量警示的 DocumentDB 帳戶名稱。

2.	在資源功能表中，按一下 [警示規則] 以開啟 [警示規則] 刀鋒視窗。![已選取 [警示規則] 組件的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb10.5.png)

3.	在 [警示規則] 刀鋒視窗中，按一下 [新增警示]。![包含反白顯示 [新增警示] 按鈕的 [警示規則] 刀鋒視窗螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb11.png)

4.	在 [Add an alert rule] 刀鋒視窗中，指定：
	*	您設定之警示規則的名稱。
	*	新警示規則的描述。
	*	警示規則的度量。
	*	決定警示何時啟動的條件、臨界值和期間。例如，過去 15 分鐘伺服器錯誤計算大於 5。
	*	警示引發時是否傳送電子郵件給服務管理員和共同管理員。
	*	警示通知的其他電子郵件地址。![[新增警示規則] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb12.png)

## 以程式設計方式監視 DocumentDB
可在入口網站中取得的帳戶層級度量 (例如，帳戶儲存體使用量和要求總數) 無法透過 DocumentDB API 取得。不過，您可以使用 DocumentDB API 來擷取集合層級的使用量資料。若要擷取集合層級的資料，請執行下列動作：

- 若要使用 REST API，請[在集合上執行 GET](https://msdn.microsoft.com/library/mt489073.aspx)。集合的配額和使用量資訊會在回應的 x-ms-resource-quota 和 x-ms-resource-usage 標頭中傳回。
- 若要使用 .NET SDK，使用 [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) 方法，其會傳回 [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx)，其中包含 **CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** 等數個使用量屬性。

若要存取其他度量，使用 [Azure Insights SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)。您可以呼叫下列程式碼來擷取可用的度量定義：

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08 

擷取個別度量的查詢會使用下列格式：

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

如需詳細資訊，請參閱 [Retrieving Resource Metrics via the Azure Insights API (透過 Azure Insights API 擷取資源度量)](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/)。

## 疑難排解
如果您的監視圖格顯示 [沒有可用的資料] 訊息，而您最近曾提出要求或將資料新增到資料庫，您就能編輯圖格以反映最近的使用量。

### 編輯磚以重新整理目前的資料
1.	若要自訂可在特定組件中顯示的度量，按一下圖表以開啟 [度量] 刀鋒視窗，然後按一下 [編輯圖表]。![[度量] 刀鋒視窗控制項的螢幕擷取畫面，其中已將 [編輯圖表] 反白顯示](./media/documentdb-monitor-accounts/madocdb3.png)

2.	在 [編輯圖表] 刀鋒視窗的 [時間範圍] 區段中，依序按一下 [過去 1 小時] 和 [確定]。![已選取過去 1 小時的 [編輯圖表] 刀鋒視窗螢幕擷取畫面](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.	您的圖格現在應該會重新整理，以顯示您目前的資料和使用量。![過去 1 小時已更新的要求總數磚的的螢幕擷取畫面](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## 後續步驟
若要深入了解 DocumentDB 容量，請參閱[管理 DocumentDB 容量](documentdb-manage.md)。

<!---HONumber=AcomDC_0831_2016-->