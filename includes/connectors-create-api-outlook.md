## 連接到 Outlook.com

### 必要條件
- Outlook.com 帳戶

您必須先授與邏輯應用程式連接到 Outlook.com 帳戶的權限，之後才能在邏輯應用程式中使用您的 Outlook.com 帳戶。所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。

若要授與邏輯應用程式連接到 Outlook.com 帳戶的權限，其步驟如下：

1. 所有的邏輯應用程式皆需由觸發程序啟動，因此在您建立邏輯應用程式之後，設計工具隨即開啟並顯示一份觸發程序清單，以供您啟動邏輯應用程式：

  ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. 在搜尋方塊中輸入 "outlook"。請注意，系統會篩選清單並列出名稱中有 "Outlook" 的所有觸發程序：![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. 選取 [Office 365 Outlook - On new email]\(Office 365 Outlook - 有新的電子郵件時)如果您之前尚未建立任何 Outlook 連線，系統會提示您提供 Outlook.com 認證。這些認證會用來授與邏輯應用程式連接並存取 Outlook.com 帳戶資料的權限：![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. 提供您的 Outlook 認證並登入：![](./media/connectors-create-api-outlook/office365-outlook-2.png) 就這麼簡單。現在您已建立 Outlook 的連線，即可在您建立的任何其他邏輯應用程式中使用這個連線。


