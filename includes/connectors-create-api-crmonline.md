#### 必要條件
* Azure 帳戶；您可以建立一個[免費帳戶](https://azure.microsoft.com/free)
* [Dynamics CRM Online](https://www.microsoft.com/zh-TW/dynamics/crm-free-trial-overview.aspx) 帳戶

在邏輯應用程式中使用您的 Dynamics 帳戶之前，請先授權該邏輯應用程式連線到您的 CRM Online 帳戶。您可以在 Azure 入口網站上，從邏輯應用程式內輕鬆完成此操作。

使用以下步驟授權邏輯應用程式連接到 CRM Online 帳戶的權限：

1. 建立邏輯應用程式。在 Logic Apps 設計工具中，從下拉式清單中選取 [顯示 Microsoft Managed API]，然後在搜尋方塊中輸入 "dynamics"。選取其中一個觸發程序或動作︰![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. 如果您之前尚未對 Dynamics 建立任何連線，系統會提示您使用 Dynamics 認證來登入：![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. 選取 [登入]，然後輸入您的使用者名稱和密碼。選取 [登入]。
   
    這些認證會用來授權邏輯應用程式連線到您的 Dynamics 帳戶，以及存取該帳戶中的資料。
4. 請注意，已建立連線。現在，在您的邏輯應用程式中繼續其他步驟：![](./media/connectors-create-api-crmonline/dynamics-properties.png)

<!---HONumber=AcomDC_0817_2016-->