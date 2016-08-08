此條件會評估每個新 Salesforce 潛在客戶的電子郵件地址欄位。如果電子郵件地址包含 *amazon.com*，條件的結果就會是 *True*。

1. 選取 [+ 新步驟]。![Salesforce 條件圖像 1](./media/connectors-create-api-salesforce/condition-1.png)
- 選取 [新增條件]。![Salesforce 條件圖像 2](./media/connectors-create-api-salesforce/condition-2.png)
- 選取 [選擇值]。![Salesforce 條件圖像 3](./media/connectors-create-api-salesforce/condition-3.png)
- 從觸發程序的潛在客戶選取 [電子郵件] 語彙基元。![Salesforce 條件圖像 4](./media/connectors-create-api-salesforce/condition-4.png)
- 選取 [包含]。![Salesforce 條件圖像 5](./media/connectors-create-api-salesforce/condition-5.png)
- 選取控制項底部的 [選擇值]。![Salesforce 條件圖像 6](./media/connectors-create-api-salesforce/condition-6.png)
- 輸入 *amazon.com* 做為您要針對新潛在客戶的電子郵件地址評估的值。如果電子郵件地址包含 *amazon.com*，條件將會評估為 *True*，邏輯應用程式中的其他步驟便能繼續執行。![Salesforce 條件圖像 7](./media/connectors-create-api-salesforce/condition-7.png)
- 儲存您的邏輯應用程式。

<!---HONumber=AcomDC_0727_2016-->