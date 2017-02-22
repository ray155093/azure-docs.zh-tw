#### <a name="prerequisites"></a>必要條件
* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* [Office 365](https://office365.com) 帳戶  

在於邏輯應用程式中使用您的 Office 365 帳戶之前，請先授權該邏輯應用程式連線到您的 Office 365 帳戶。 您可以在 Azure 入口網站上，從邏輯應用程式內輕鬆完成此操作。  

請使用下列步驟來授權邏輯應用程式連線到您的 Office 365 帳戶：

1. 建立邏輯應用程式。 在 Logic Apps 設計工具中，從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "office 365"。 選取其中一個觸發程序或動作︰  
    ![Office 365 連線建立步驟](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. 如果您之前尚未建立與 Office 365 的任何連線，系統將會提示您使用 Office 365 認證來進行登入：  
    ![Office 365 連線建立步驟](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. 選取 [登入]，然後輸入您的使用者名稱和密碼。 選取 [登入]：  
    ![Office 365 連線建立步驟](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    這些認證會用來授權邏輯應用程式連線及存取您的 Office 365 帳戶。 
4. 請注意，已建立連線。 現在，請繼續進行您邏輯應用程式中的其他步驟：   
    ![Office 365 連線建立步驟](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  



<!--HONumber=Nov16_HO3-->


