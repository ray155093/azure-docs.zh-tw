### 必要條件

- [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) 帳戶  


您必須先授權邏輯應用程式連接到您的 SMTP 帳戶，才可以在邏輯應用程式中使用您的 SMTP 帳戶。幸運的是，您可以輕鬆地在 Azure 入口網站上從邏輯應用程式內完成。

若要授與邏輯應用程式連接到 SMTP 帳戶的權限，其步驟如下：
1. 若要建立 SMTP 連接，請在邏輯應用程式設計工具中，選取下拉式清單的 [Show Microsoft managed APIs] \(顯示 Microsoft Managed API)，然後在搜尋方塊中輸入 *SMTP*。選取您要使用的觸發程序或動作：![](./media/connectors-create-api-smtp/smtp-1.png)  
2. 如果您之前尚未建立任何 SMTP 連線，系統會提示您提供 SMTP 認證。這些認證會用來授與邏輯應用程式連接並存取 SMTP 帳戶資料的權限：![](./media/connectors-create-api-smtp/smtp-2.png)  
3. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：![](./media/connectors-create-api-smtp/smtp-3.png)  

<!---HONumber=AcomDC_0525_2016-->