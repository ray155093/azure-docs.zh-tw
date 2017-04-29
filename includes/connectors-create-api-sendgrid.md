### <a name="prerequisites"></a>必要條件
* [SendGrid](https://www.SendGrid.com/) 帳戶 

您必須先授與邏輯應用程式連接到 SendGrid 帳戶的權限，之後才能在邏輯應用程式中使用您的 SendGrid 帳戶。 所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。 

若要授與邏輯應用程式連接到 SendGrid 帳戶的權限，其步驟如下：

1. 若要建立 SendGrid 連線，請在邏輯應用程式設計工具中，選取下拉式清單的 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入「SendGrid」。 選取您要使用的觸發程序或動作：  
   ![SendGrid 步驟 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. 如果您之前尚未建立任何 SendGrid 連線，系統會提示您提供 SendGrid 認證。 這些認證會用來授與邏輯應用程式連接並存取 SendGrid 帳戶資料的權限：  
   ![SendGrid 步驟 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：  
   ![SendGrid 步驟 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

