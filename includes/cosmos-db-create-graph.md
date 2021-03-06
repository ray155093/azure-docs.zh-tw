您現在可以使用 [資料總管] 來建立圖形容器，並將資料新增至您的資料庫。 

1. 在 Azure 入口網站的導覽功能表中，按一下 [資料總管]。 
2. 在 [資料總管] 刀鋒視窗中，按一下 [新增圖形]，然後使用下列資訊填入頁面。

    ![Azure 入口網站中的資料總管](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    設定|建議的值|說明
    ---|---|---
    資料庫識別碼|sample-database|新資料庫的識別碼。 資料庫名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。
    圖形識別碼|sample-graph|新圖形的識別碼。 圖形名稱與資料庫識別碼具有相同的字元需求。
    儲存體容量| 10 GB|保留預設值。 這是資料庫的儲存體容量。
    輸送量|400 RU|保留預設值。 如果您想要降低延遲，稍後可以相應增加輸送量。
    RU/m|關閉|保留預設值。 如果您稍後需要處理 spiky 工作負載，您可以屆時開啟 [RU/m](../articles/cosmos-db/request-units-per-minute.md) 功能。
    資料分割索引鍵|/userid|可將資料平均分散到每個資料分割的資料分割索引鍵。 選取正確的資料分割索引鍵對於建立效能圖來說很重要，請參閱[設計資料分割](../articles/cosmos-db/partition-data.md#designing-for-partitioning)。

3. 填妥表單後，按一下 [確定]。