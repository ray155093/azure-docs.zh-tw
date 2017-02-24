在此逐步解說中，您將了解如何使用「Salesforce - 當建立物件時」  觸發程序，在於 Salesforce 中建立新潛在客戶時，起始邏輯應用程式工作流程。

> [!NOTE]
> 如果您尚未建立與 Salesforce 的「連線」，系統將會提示您登入您的 Salesforce 帳戶。  
> 
> 

1. 在邏輯應用程式設計工具的搜尋方塊中輸入 salesforce，然後選取 **Salesforce - 當建立物件時**觸發程序。  
   ![Salesforce 觸發程序圖像 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. [當建立物件時]  控制項隨即顯示。  
   ![Salesforce 觸發程序圖像 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. 選取 [物件類型] 然後從清單的物件中選取 [潛在客戶]。 在此步驟中，您會指出您要建立一個觸發程序，此觸發程序將在每次 Salesforce 中有建立新潛在客戶的情況時，通知您的邏輯應用程式。   
   ![Salesforce 觸發程序圖像 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. 就這麼簡單。 您已建立觸發程序。 不過，您必須至少建立一個動作，才能讓此邏輯應用程式變成有效。    
   ![Salesforce 觸發程序圖像 4](./media/connectors-create-api-salesforce/trigger-4.png)   

此時，邏輯應用程式已設有觸發程序，該觸發程序會在 Salesforce 中有建立新項目的情況時，開始執行工作流程中的其他觸發程序和動作。  



<!--HONumber=Nov16_HO3-->


