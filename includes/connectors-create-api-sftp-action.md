既然您已新增觸發程序，現在即可使用觸發程序所產生的資料來進行一些有趣的操作。 請依照下列步驟來新增「SFTP - 擷取資料夾」  動作。 當符合定義的條件時，這個動作就會擷取檔案的內容。 

若要設定此動作，您將必須提供下列資訊。 您會發現，使用觸發程序所產生的資料做為一些新檔案屬性的輸入相當容易︰

| SFTP - 擷取資料夾屬性 | 說明 |
| --- | --- |
| 來源封存檔案路徑 |這是所要擷取檔案的路徑。 您可以從先前的動作選取其中一個語彙基元，或瀏覽 SFTP 伺服器來尋找檔案路徑。 |
| 目的地資料夾路徑 |這是將放置所擷取檔案的路徑。 您可以從先前的動作選取其中一個語彙基元做為目的地路徑，或瀏覽 SFTP 伺服器並選取路徑。 |
| 覆寫？ |指出如果在目的地資料夾路徑中找到與所擷取檔案同名的檔案，是否應該覆寫現有的檔案。 |

讓我們開始新增要在先前定義的條件評估為 *True*時擷取檔案的動作。 

1. 選取 [新增動作] 。        
   ![SFTP 動作條件圖像 6](./media/connectors-create-api-sftp/condition-6.png)   
2. 選取 [SFTP - 擷取資料夾] 動作      
   ![SFTP 動作條件圖像 7](./media/connectors-create-api-sftp/condition-7.png)   
3. 選取**來源封存檔案路徑**              
   ![SFTP 動作條件圖像 9](./media/connectors-create-api-sftp/condition-9.png)   
4. 選取 [檔案路徑]  語彙基元。 這表示您將使用觸發程序所找到檔案的檔案路徑做為來源封存檔案路徑。           
   ![SFTP 動作條件圖像 10](./media/connectors-create-api-sftp/condition-10.png)   
5. 選取**目的地資料夾路徑**           
   ![SFTP 動作條件圖像 11](./media/connectors-create-api-sftp/condition-11.png)   
6. 選取 [檔案路徑]  語彙基元。 這表示您將使用觸發程序所找到檔案的檔案路徑做為所擷取檔案的目的地路徑。   
7. 在 [目的地資料夾路徑] 控制項中，輸入 \ExtractedFile。 請在 [目的地資料夾路徑] 控制項中緊接 [檔案路徑] 語彙基元之後執行這項操作。         
   ![SFTP 動作條件圖像 12](./media/connectors-create-api-sftp/condition-12.png)   
8. 在 *[覆寫？] 控制項中輸入 True，以指示當現有檔案與所擷取檔案同名時應該覆寫現有檔案。      
   ![SFTP 動作條件圖像 13](./media/connectors-create-api-sftp/condition-13.png)   
9. 儲存對您工作流程所做的變更  

