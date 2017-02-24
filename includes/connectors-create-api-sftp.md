### <a name="prerequisites"></a>必要條件
* [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) 帳戶  

您必須先授權邏輯應用程式連線到您的 SFTP 帳戶，才能在該邏輯應用程式中使用您的 SFTP 帳戶。 所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。  

若要授權邏輯應用程式連線到您的 SFTP 帳戶，其步驟如下：  

1. 若要建立與 SFTP 的連線，請在邏輯應用程式設計工具中，從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入「SFTP」。 選取 [SFTP - 當新增或修改檔案時] 觸發程序：  
   ![SFTP 線上連線圖像 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. 如果您之前尚未建立任何 SFTP 連接，系統會提示您提供 SFTP 認證。 這些認證將用來授權邏輯應用程式連線及存取您 SFTP 帳戶的資料：  
   ![SFTP 線上連線圖像 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：   
   ![SFTP 線上連線圖像 3](./media/connectors-create-api-sftp/sftp-3.png) 



<!--HONumber=Nov16_HO3-->


