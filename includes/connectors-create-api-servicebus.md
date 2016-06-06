### 必要條件

- [ServiceBus](https://azure.microsoft.com/services/service-bus/) 帳戶  


您必須先授權邏輯應用程式連接到您的 ServiceBus 帳戶，才可以在邏輯應用程式中使用您的 ServiceBus 帳戶。幸運的是，您可以輕鬆地在 Azure 入口網站上從邏輯應用程式內完成。

若要授與邏輯應用程式連接到 ServiceBus 帳戶的權限，其步驟如下：
1. 若要建立 ServiceBus 連接，請在邏輯應用程式設計工具中，選取下拉式清單的 [Show Microsoft managed APIs] (顯示 Microsoft Managed API)，然後在搜尋方塊中輸入 *ServiceBus *。選取您要使用的觸發程序或動作：![](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. 如果您之前尚未建立任何 ServiceBus 連接，系統會提示您提供 ServiceBus 認證。這些認證會用來授與邏輯應用程式連接並存取 ServiceBus 帳戶資料的權限：![](./media/connectors-create-api-servicebus/servicebus-2.png)  
3. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：![](./media/connectors-create-api-servicebus/servicebus-3.png)   

<!---HONumber=AcomDC_0525_2016-->