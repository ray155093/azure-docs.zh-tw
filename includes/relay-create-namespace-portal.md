1. 登入 [Azure 入口網站][Azure portal]。
2. 在入口網站的左方瀏覽窗格中，依序按一下 [新增]、[企業整合] 及 [轉送]。
3. 在 [建立命名空間]  對話方塊中，輸入命名空間名稱。 系統會立即檢查此名稱是否可用。
4. 在 [訂用帳戶]  欄位中，選擇要在其中建立命名空間的 Azure 訂用帳戶。
5. 在 [資源群組]**[](../articles/azure-portal/resource-group-portal.md)** 欄位中，選擇將存留命名空間的現有資源群組，或是建立新的資源群組。      
6. 在 [位置] 中，選擇應裝載您命名空間的國家或地區。
   
    ![建立命名空間][create-namespace]
7. 按一下 [建立] 。 此時系統會建立並啟用命名空間。 系統為帳戶提供資源時，您可能需要等幾分鐘。

### <a name="obtain-the-management-credentials"></a>取得管理認證
1. 在命名空間清單中，按一下新建立的命名空間名稱。
2. 在命名空間刀鋒視窗中，按一下 [共用存取原則]。
3. 在 [共用存取原則] 刀鋒視窗中，按一下 **RootManageSharedAccessKey**。
   
    ![connection-info][connection-info]
4. 在 [原則: RootManageSharedAccessKey] 刀鋒視窗中，按一下 [連接字串 - 主要金鑰] 旁邊的複製按鈕，將連接字串複製到剪貼簿以供稍後使用。 將此值貼到記事本或一些其他暫存位置。
   
    ![connection-string][connection-string]

5. 重複前一個步驟，複製 [主要金鑰] 的值並貼到暫存位置以供稍後使用。  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure portal]: https://portal.azure.com


<!--HONumber=Feb17_HO1-->


