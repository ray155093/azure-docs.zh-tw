您現在可以使用 [資料總管] 將資料新增至您的新集合。

1. 在 [資料總管] 中，新的資料庫會出現在 [集合] 窗格中。 依序展開 [項目] 資料庫、[ToDoList] 集合，按一下 [文件]，然後按一下 [新增文件]。 

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/cosmos-db-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. 現在將一些新文件新增至具有下列結構的集合，您會在其中插入每份文件中唯一的識別碼值並且視需要變更其他屬性。 新文件可擁有您想要的任何結構，因為 Azure Cosmos DB 不會對您的資料強加任何結構描述。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     您現在可以在 資料總管 中使用查詢來擷取您的資料。 [資料總管] 預設會使用 SELECT * FROM c 來擷取集合中的所有文件，但您可以將其變更為 SELECT * FROM c ORDER BY c.name ASC，以 name 屬性的字母遞增順序傳回所有文件。 
 
     您也可以使用 [資料總管] 來建立預存程序、UDF 和觸發程序，以執行伺服器端商務邏輯。 [資料總管] 會公開 API 中所有可用的內建程式設計資料存取，但可讓您在 Azure 入口網站中輕鬆存取您的資料。