#### 必要條件
- Azure 帳戶；您可以建立一個[免費帳戶](https://azure.microsoft.com/free)
- [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 帳戶

您必須先授權邏輯應用程式連接到 OneDrive 帳戶的權限，之後才能在邏輯應用程式中使用您的 OneDrive 帳戶。所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。

使用以下步驟授權邏輯應用程式連接到 OneDrive 帳戶的權限：

1. 建立邏輯應用程式。在 Logic Apps 設計工具中，選取下拉式清單的 [Show Microsoft managed APIs] \(顯示 Microsoft Managed API)，然後在搜尋方塊中輸入「OneDrive」。選取其中一個觸發程序或動作︰![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 如果您之前尚未對 OneDrive 建立任何連線，系統會提示您使用 OneDrive 認證來登入：![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. 選取 [登入]，然後輸入您的使用者名稱和密碼。選取 [登入]：![](./media/connectors-create-api-onedrive/onedrive-3.png)

	這些認證是用來授權邏輯應用程式連接 OneDrive 帳戶並存取帳戶中資料的權限。
4. 選取 [是] 以授權邏輯應用程式使用您的 OneDrive 帳戶︰![](./media/connectors-create-api-onedrive/onedrive-4.png)
5. 請注意，已建立連線。現在，在您的邏輯應用程式中繼續其他步驟：![](./media/connectors-create-api-onedrive/onedrive-5.png)

<!---HONumber=AcomDC_0720_2016-->