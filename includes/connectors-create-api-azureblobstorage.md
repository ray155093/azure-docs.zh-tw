### 必要條件

- [Azure Blob 儲存體](https://azure.microsoft.com/documentation/services/storage/)帳戶  


您必須先授權邏輯應用程式連接到您的 Azure Blob 儲存體帳戶，才可以在邏輯應用程式中使用您的 Azure Blob 儲存體帳戶。幸運的是，您可以輕鬆地在 Azure 入口網站上從邏輯應用程式內完成。

若要授與邏輯應用程式連接到 Azure Blob 儲存體帳戶的權限，其步驟如下：
1. 若要建立 Azure Blob 儲存體連接，請在邏輯應用程式設計工具的下拉式清單中選取 [Show Microsoft managed APIs] (顯示 Microsoft Managed API)，然後在搜尋方塊中輸入「Azure Blob 儲存體」。選取您要使用的觸發程序或動作：![Azure Blob 儲存體連接的建立步驟](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. 如果您之前尚未建立任何 Azure Blob 儲存體連接，系統會提示您提供 Azure Blob 儲存體認證。這些認證會用來授與邏輯應用程式連接並存取 Azure Blob 儲存體帳戶資料的權限：![Azure Blob 儲存體連接的建立步驟](./media/connectors-create-api-azureblobstorage/azureblobstorage-2.png)  
3. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：![Azure Blob 儲存體連接的建立步驟](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

<!---HONumber=AcomDC_0525_2016-->