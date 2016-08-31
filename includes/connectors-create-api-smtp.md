### 必要條件

- [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) 帳戶  


您必須先授權邏輯應用程式連線到您的 SMTP 帳戶，才能在該邏輯應用程式中使用您的 SMTP 帳戶。幸運的是，您可以在「Azure 入口網站」上，從邏輯應用程式內輕鬆完成此操作。  

若要授權邏輯應用程式連線到您的 SMTP 帳戶，其步驟如下：  
1. 若要建立與 SMTP 的連線，請在邏輯應用程式設計工具中，從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 *SMTP*。請選取您想要使用的觸發程序或動作：  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. 如果您之前尚未建立任何 SMTP 連線，系統會提示您提供 SMTP 認證。這些認證將用來授權邏輯應用程式連線及存取您 SMTP 帳戶的資料：  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. 請注意，連線已經建立，您現在可以繼續進行邏輯應用程式中的其他步驟：  
![](./media/connectors-create-api-smtp/smtp-3.png)  

<!---HONumber=AcomDC_0727_2016-->
