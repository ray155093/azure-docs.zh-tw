<properties
	pageTitle="在邏輯應用程式中新增查詢動作 | Microsoft Azure"
	description="執行如篩選陣列等動作的查詢動作概觀。"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# 開始使用查詢動作

您可以使用查詢動作來處理完成以下工作流程的批次和陣列︰

- 從資料庫建立所有高優先順序記錄的工作。
- 將電子郵件的所有 PDF 附件儲存到 Azure Blob。

若要開始在邏輯應用程式中使用查詢動作，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

---

## 使用查詢動作
	
動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作。](connectors-overview.md) 查詢動作目前在設計工具中公開一項作業 - 篩選陣列。這可讓您查詢陣列並傳回一組篩選結果。以下說明如何將它新增至邏輯應用程式︰

1. 選取 [新增步驟] 按鈕
1. 選擇 [新增動作]
1. 在動作搜尋方塊中，輸入「篩選」以列出**篩選陣列**動作

	![選取查詢動作](./media/connectors-native-query/using-action-1.png)

1. 選取要篩選的陣列 (以下螢幕擷取畫面顯示來自 Twitter 搜尋的結果陣列)
1. 建立條件來評估每個項目 (以下螢幕擷取畫篩選出有 100 個以上關注者的使用者推文)

	![完整查詢動作](./media/connectors-native-query/using-action-2.png)

1. 此動作會輸出新的陣列，其中僅包含符合篩選條件需求的結果
1. 按一下工具列左上角的 [儲存]，邏輯應用程式便會儲存並發佈 (啟動)

---

## 技術詳細資訊

以下是此連接器支援的動作的詳細資料。

## 查詢動作

動作是由邏輯應用程式中定義的工作流程所執行的作業。[深入了解動作。](connectors-overview.md) 連接器有 1 個可能的動作。

|動作|說明|
|---|---|
|篩選陣列|評估陣列中的每個項目是否符合條件並傳回結果|

### 動作詳細資料

查詢動作隨附 1 個可能的動作。下面有關於每個動作、其必要和選擇性輸入欄位，以及與其使用方式相關聯的對應輸出詳細資料的資訊。

#### 篩選陣列
提出 HTTP 輸出要求。* 代表必要欄位。

|顯示名稱|屬性名稱|說明|
|---|---|---|
|從*|from|要篩選的陣列|
|條件*|其中|要針對每個項目評估的條件|
<br>

**輸出詳細資料**

HTTP 回應

|屬性名稱|資料類型|說明|
|---|---|---|
|篩選的陣列|array|陣列，其中包含每筆篩選結果的物件|

---

## 後續步驟

以下是有關如何推動邏輯應用程式的詳細資料和我們的社群。

## 建立邏輯應用程式

立即試用平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。您可以查看我們的 [API 清單](apis-list.md)，以探索邏輯應用程式中其他可用的連接器。

<!---HONumber=AcomDC_0727_2016-->