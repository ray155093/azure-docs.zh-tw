讓我們新增一個觸發程序。

1. 在邏輯應用程式設計工具的搜尋方塊中輸入 sftp，然後選取 [SFTP - 當新增或修改檔案時] 觸發程序   
   ![SFTP 觸發程序圖像 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. [當新增或修改檔案時] 控制項隨即開啟  
   ![SFTP 觸發程序圖像 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. 選取位於控制項右側的 [...]  。 這會開啟資料夾選擇器控制項   
   ![SFTP 觸發程序圖像 3](./media/connectors-create-api-sftp/action-1.png)  
4. 選取 [SFTP]  以選取根資料夾，做為要監視是否有新的或已修改之檔案的資料夾。 請注意，根資料夾現在已顯示在 [資料夾]  控制項中。  
   ![SFTP 觸發程序圖像 4](./media/connectors-create-api-sftp/action-2.png)   

此時，邏輯應用程式已設有觸發程序，該觸發程序會在特定 SFTP 資料夾中有修改或建立資料夾的情況時，開始執行工作流程中的其他觸發程序和動作。 

> [!NOTE]
> 若要讓邏輯應用程式能夠運作，它必須至少包含一個觸發程序和一個動作。 請依照下一節中的步驟來新增動作。  
> 
> 

