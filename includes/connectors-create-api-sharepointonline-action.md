既然您已新增觸發程序，現在即可使用觸發程序所產生的資料來進行一些有趣的操作。 請依照下列步驟來新增「SharePoint Online-建立檔案」  動作。 這個動作會在每次引發新增項目觸發程序時，在 SharePoint Online 中建立檔案。 

若要設定此動作，您將必須提供下列資訊。 當您提供此資訊時，您會發現，使用觸發程序所產生的資料做為一些新檔案屬性的輸入相當容易︰

| 建立檔案屬性 | 說明 |
| --- | --- |
| 網站 URL |這是您要在其中建立新檔案之 SharePoint Online 網站的 URL。 從顯示的清單中選取網站。 |
| 資料夾路徑 |這是其中將放置新檔案的資料夾 (位於前一個步驟中選取的「網站 URL」)。 瀏覽並選取資料夾。 |
| 檔案名稱 |這是所要建立之檔案的名稱。 |
| 檔案內容 |將寫入檔案中的內容。 |

1. 選取 [+ 新步驟]  來新增動作。  
   ![SharePoint Online 動作圖像 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. 選取 [新增動作]  連結。 這會開啟搜尋方塊，您可以在其中搜尋任何想要採取的動作。 以這個範例來說，感興趣的動作是 SharePoint 動作。    
   ![SharePoint Online 動作圖像 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. 輸入 *sharepoint* 來搜尋與 SharePoint 相關的動作。
4. 選取 [SharePoint Online-建立檔案] 做為要採取的動作。   **注意**：如果您先前尚未建立與 SharePoint Online 的連線，系統將會提示您授權邏輯應用程式存取您的 SharePoint 帳戶。    
   ![SharePoint Online 動作圖像 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. [建立檔案]  控制項隨即開啟。   
   ![SharePoint Online 動作圖像 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. 選取 [網站 URL]  ，然後瀏覽以尋找您想要在其中建立檔案的網站。     
   ![SharePoint Online 動作圖像 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. 選取 [資料夾路徑]  ，然後瀏覽以尋找將在其中放置新檔案的資料夾。  
   ![SharePoint Online 動作圖像 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. 選取 [檔案名稱]  控制項，然後輸入您所要建立檔案的名稱。 在這裡，您可以直接輸入檔案名稱，也可以使用來自您先前所建立觸發程序的任何屬性。 若要這麼做，可以從 [當新項目建立時產生的輸出] 清單中選取屬性。 此清單只有在您選取 [檔案名稱]  控制項之後才會顯示。 在此逐步解說中，我選取了識別碼 (新清單項目的識別碼) 做為「SharePoint Online - 建立檔案」  動作所要建立檔案的名稱。    
   ![SharePoint Online 動作圖像 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. 選取 [檔案內容]  控制項，然後輸入將寫入所要建立檔案中的內容。 針對檔案內容，請注意，您可以使用來自您先前所建立觸發程序的任何屬性。 請直接從顯示的清單中選取屬性。 或者，您也可以在控制項中直接輸入「檔案內容」  文字。 在此範例中，我選取了一些屬性，並在每個屬性之間加入空格和連字號。        
   ![SharePoint Online 動作圖像 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. 儲存對您工作流程所做的變更  
11. 恭喜，您現在已經有一個功能完整的邏輯應用程式，當新項目被新增到 SharePoint Online 清單中時，就會觸發此應用程式。 此應用程式會接著使用來自新清單項目的一些屬性來建立檔案。  您現在可以在 SharePoint 清單中建立新項目來測試它。 



<!--HONumber=Nov16_HO3-->


