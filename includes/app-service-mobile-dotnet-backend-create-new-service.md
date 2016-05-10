1. 登入 [Azure 入口網站]。

2. 按一下 [+ 新增] > [Web + 行動] > [行動應用程式]，然後為您的行動應用程式後端提供名稱。

3. 在 [資源群組] 中選取現有資源群組或建立新的群組 (使用和應用程式相同的名稱)。
 
4. 在 [App Service 方案] 中選取現有 App Service 方案，或[建立新的方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan)。按一下 [建立]。這會建立行動應用程式後端。App Service 方案的設定會決定與您的應用程式相關聯的[位置、功能、成本和計算資源](https://azure.microsoft.com/pricing/details/app-service/)。
	
6. 在新行動應用程式後端的 [設定] 刀鋒視窗中，按一下 [快速啟動] > 您的用戶端應用程式平台 > [連接資料庫]。

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. 在 [加入資料連接] 刀鋒視窗中，按一下 [SQL Database] > [建立新的資料庫]，輸入資料庫**名稱**，選擇定價層，然後按一下 [伺服器]。您可以重複使用這個新資料庫。如果您在同樣的位置內已有資料庫，則可以改為選擇 [使用現有的資料庫]。不建議您使用位在不同位置的資料庫，因為這會需要頻寬成本和產生更高的延遲。
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. 在 [新增伺服器] 刀鋒視窗的 [伺服器名稱] 欄位中輸入唯一的伺服器名稱、提供登入和密碼、勾選 [允許 Azure 服務存取伺服器]，然後按一下 [確定]。這會建立新的資料庫。

9. 回到 [加入資料連接] 刀鋒視窗中，按一下 [連接字串]、輸入您的資料庫登入和密碼值，然後按一下 [確定]。等候幾分鐘的時間讓資料庫成功部署，然後再繼續進行。

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->