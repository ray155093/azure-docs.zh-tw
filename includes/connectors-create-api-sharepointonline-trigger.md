在此範例中，我將說明如何使用「SharePoint Online - 當建立新項目時」  觸發程序，在於 SharePoint Online 清單中建立新項目時，起始邏輯應用程式工作流程。

> [!NOTE]
> 如果您尚未建立與 SharePoint Online 的「連線」，系統將會提示您登入您的 SharePoint 帳戶。  
> 
> 

1. 在邏輯應用程式設計工具的搜尋方塊中輸入 sharepoint，然後選取 [SharePoint Online - 當建立新項目時] 觸發程序。  
   ![SharePoint Online 觸發程序映像](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. [當建立新項目時]  控制項隨即顯示。  
   ![SharePoint Online 觸發程序圖像 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. 選取 [網站 URL] 。 這是您想要監視是否有要觸發工作流程之新項目的 SharePoint Online 網站。  
   ![SharePoint Online 觸發程序圖像 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. 選取一個「清單名稱」 。 這是您想要監視是否有將觸發工作流程之新項目的 SharePoint Online 網站上清單。  
   ![SharePoint Online 觸發程序圖像 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

此時，邏輯應用程式已設有觸發程序，該觸發程序會開始執行工作流程中的其他觸發程序和動作。 這會在每次您選取的 SharePoint Online 清單中有建立新項目的情況時發生。  

