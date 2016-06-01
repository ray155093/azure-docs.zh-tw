### 必要條件
- Twilio 帳戶
- 已驗證的 Twilio 電話號碼，以便接收簡訊
- 已驗證的 Twilio 電話號碼，以便傳送簡訊

>[AZURE.NOTE] 如果您是使用 Twilio 試用帳戶，則只能傳送簡訊給**已驗證**的電話號碼。

您必須先授與邏輯應用程式連接到 Twilio 帳戶的權限，之後才能在邏輯應用程式中使用您的 Twilio 帳戶。所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。

若要授與邏輯應用程式連接到 Twilio 帳戶的權限，其步驟如下：

1. 若要建立 Twilio 連線，請在邏輯應用程式設計工具中，選取下拉式清單的 [Show Microsoft managed APIs]\(顯示 Microsoft Managed API)，然後在搜尋方塊中輸入 *Twilio*。選取您要使用的觸發程序或動作：![](./media/connectors-create-api-twilio/twilio-0.png)
2. 如果您之前尚未建立任何 Twilio 連線，系統會提示您提供 Twilio 認證。這些認證會用來授與邏輯應用程式連接並存取 Twilio 帳戶資料的權限：![](./media/connectors-create-api-twilio/twilio-1.png)  
3. 您將需要提供 Twilio 儀表板的 **Twilio 帳戶識別碼**和 **Twilio 存取權杖**，因此請立即登入 Twilio 帳戶並擷取這兩項資訊：![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio 及邏輯應用程式會使用不同的名稱來識別這兩項資訊。您必須依據下列方法將其對應至邏輯應用程式對話方塊：![](./media/connectors-create-api-twilio/twilio-3.png)  
5. 選取 [建立連線] 按鈕：![](./media/connectors-create-api-twilio/twilio-4.png)
6. 請注意，此時已建立連線，您現可進行邏輯應用程式中的其他步驟：![](./media/connectors-create-api-twilio/twilio-5.png)