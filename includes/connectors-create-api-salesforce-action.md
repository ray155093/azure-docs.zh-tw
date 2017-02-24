既然您已新增條件，現在即可使用觸發程序所產生的資料來進行一些有趣的操作。 請依照下列步驟來新增「Salesforce - 取得物件」  動作。 這個動作會在每次建立新潛在客戶時取得資料。 您也將新增第二個動作，此動作會使用來自「Salesforce - 取得物件」動作的資料，以使用 Office 365 連接器來傳送電子郵件。  

若要設定此動作，您將必須提供下列資訊。 您會發現，使用觸發程序所產生的資料做為一些新檔案屬性的輸入相當容易︰

| 建立檔案屬性 | 說明 |
| --- | --- |
| 物件類型 |這是您感興趣的 Salesforce 物件類型。 範例包括潛在客戶、帳戶等。 |
| 物件識別碼 |這代表物件的識別碼。 |

1. 選取 [新增動作]  連結。 這會開啟搜尋方塊，您可以在其中搜尋任何想要採取的動作。 以這個範例來說，感興趣的動作是 Salesforce 動作。      
   ![Salesforce 動作圖像 1](./media/connectors-create-api-salesforce/action-1.png)  
2. 輸入 *salesforce* 來搜尋與 salesforce 相關的動作。
3. 選取 [Salesforce - 取得物件] 做為要採取的動作。   **注意**：如果您先前尚未授權邏輯應用程式存取您的 Salesforce 帳戶，系統將會提示您這麼做。    
   ![Salesforce 動作圖像 2](./media/connectors-create-api-salesforce/action-2.png)    
4. [取得物件]  控制項隨即開啟。  
5. 選取 [潛在客戶]  做為物件類型。
6. 選取 [物件識別碼]  控制項。
7. 選取 [...]  以展開可用來做為動作輸入的語彙基元清單。       
   ![Salesforce 動作圖像 3](./media/connectors-create-api-salesforce/action-3.png)    
8. 選取 [潛在客戶識別碼]  控制項。   
   ![Salesforce 動作圖像 4](./media/connectors-create-api-salesforce/action-4.png)     
9. 請注意，[潛在客戶識別碼] 語彙基元現在位於 [物件識別碼] 控制項中，這表示「取得物件」動作會搜尋識別碼與觸發此邏輯應用程式之潛在客戶的識別碼相等的潛在客戶。  
   ![Salesforce 動作圖像 5](./media/connectors-create-api-salesforce/action-5.png)  
10. 儲存您的工作。 就這麼簡單，您已將「取得物件」動作新增到邏輯應用程式中。 您的「取得物件」控制項看起來應該會像這樣︰    
    ![Salesforce 動作圖像 6](./media/connectors-create-api-salesforce/action-6.png)  

既然您已新增動作來取得潛在客戶，您可能會想要使用新建立的潛在客戶來執行一些有趣的操作。 在企業中，您可能要想要傳送電子郵件，來通知通訊群組清單已建立新的潛在客戶。 讓我們使用 Office 365 連接器，來傳送含有來自 Salesforce 中新潛在客戶物件一些相關資訊的電子郵件。  

1. 選取 [新增動作]，然後在搜尋控制項中輸入「電子郵件」。 這會篩選出與傳送和接收電子郵件相關的動作。  
2. 選取 [Office 365 Outlook - 傳送電子郵件]  清單項目。 如果您尚未建立與 Office 365 帳戶的「連線」  ，系統就會提示您輸入 Office 365 認證來立即建立連線。 完成之後，[傳送電子郵件]  控制項會隨即開啟。        
   ![Salesforce 動作圖像 7](./media/connectors-create-api-salesforce/action-7.png)  
3. 在 [收件者]  控制項中，輸入要接收您電子郵件的電子郵件地址。
4. 在 [主旨] 控制項中，輸入「已建立新的潛在客戶」，然後選取 [公司] 語彙基元。 這會顯示來自在 Salesforce 中建立之新潛在客戶的 [公司]  欄位。  
5. 在 [內文]  控制項中，您可以從新潛在客戶物件選取任何語彙基元，也可以輸入您想要顯示在電子郵件內文中的任何文字。 以下是範例：  
   ![Salesforce 動作圖像 8](./media/connectors-create-api-salesforce/action-8.png)   
6. 儲存您的工作流程。  

就這麼簡單。 您的邏輯應用程式現在已完成。  

現在，您可以測試邏輯應用程式︰在 Salesforce 中，建立一個符合您所建立條件的新潛在客戶。  如果您完全按照本逐步解說操作，就只要建立一個電子郵件地址包含 *amazon.com* 的新潛在客戶即可。 幾秒鐘之後，應該會觸發您的邏輯應用程式，而結果看起來可能會像這樣︰  
![Salesforce 動作圖像 9](./media/connectors-create-api-salesforce/action-9.png)  



<!--HONumber=Nov16_HO3-->


