### 必要條件

您必須具有[服務匯流排](https://azure.microsoft.com/services/service-bus/)帳戶。

您必須先授權邏輯應用程式連線到您的服務匯流排帳戶，才能在該邏輯應用程式中使用您的「Azure 服務匯流排」帳戶。所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。

若要授權邏輯應用程式連線到您的服務匯流排帳戶，其步驟如下：

1. 若要建立服務匯流排連線，請在邏輯應用程式設計工具的下拉式清單中選取 [顯示 Microsoft 管理的 API]。然後在搜尋方塊中輸入**服務匯流排**。選取您想要使用的觸發程序或動作。  
    ![服務匯流排連線圖像 1](./media/connectors-create-api-servicebus/servicebus-1.png)  

2. 如果您之前尚未建立與服務匯流排的任何連線，系統將會提示您提供您的服務匯流排認證。這些認證將用來授權邏輯應用程式連線及存取您服務匯流排帳戶的資料。服務匯流排連接器需要服務匯流排命名空間的連接字串。它也需要**管理**權限。一個可辨別您的連接字串是否適用於命名空間或特定實體的理想方式就是，它是否包含 `EntityPath` 參數。如果包含，它就不是邏輯應用程式的適用連接字串。  
    ![服務匯流排連接字串](./media/connectors-create-api-servicebus/connectionstring.png)

1. 在您收到的命名空間的連接字串之後，您可以將它用於 Logic Apps 中的「API 連線」。  
    ![服務匯流排連線圖像 2](./media/connectors-create-api-servicebus/servicebus-2.png)

3. 請注意，連線已經建立，您現在可以繼續進行邏輯應用程式中的其他步驟。  
    ![服務匯流排連線圖像 3](./media/connectors-create-api-servicebus/servicebus-3.png)   

<!-----HONumber=AcomDC_0810_2016------>