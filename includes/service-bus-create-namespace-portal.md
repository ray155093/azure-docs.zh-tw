1. 登入 [Azure 入口網站][]。

2. 在入口網站的左方瀏覽窗格中，依序按一下 [新增]，[企業整合] 及 [服務匯流排]。

4. 在 [建立命名空間] 對話方塊中，輸入命名空間名稱。系統會立即檢查此名稱是否可用。

5. 確定命名空間名稱可用之後，請選擇定價層 ([基本]、[標準] 或 [進階])。

7. 在 [訂用帳戶] 欄位中，選擇要在其中建立命名空間的 Azure 訂用帳戶。

9. 在 [資源群組] 欄位中，選擇將存留命名空間的現有資源群組，或是建立新的資源群組。

8. 在 [位置] 中，選擇應裝載您的命名空間的國家或區域。

	![建立命名空間][create-namespace]

6. 按一下 [建立]。此時系統會建立並啟用命名空間。系統為帳戶提供資源時，您可能需要等幾分鐘。
 
### 取得管理認證

1. 在命名空間清單中，按一下新建立的命名空間名稱。
 
3. 在命名空間刀鋒視窗中，按一下 [共用存取原則]。

4. 在 [共用存取原則] 刀鋒視窗中，按一下 **RootManageSharedAccessKey**。

	![connection-info][connection-info]

5. 在 [原則: RootManageSharedAccessKey] 刀鋒視窗中，按一下 [連接字串 – 主索引鍵] 旁邊的複製按鈕，將連接字串複製到剪貼簿以供稍後使用。將此值貼到記事本或一些其他暫存位置。

	![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure 入口網站]: https://portal.azure.com

<!---HONumber=AcomDC_0921_2016-->