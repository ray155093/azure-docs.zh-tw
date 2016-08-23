<properties
	pageTitle="透過 C# 查詢連接到 SQL Database | Microsoft Azure"
	description="以 C# 撰寫程式以查詢和連接到 SQL Database。有關 IP 位址、連接字串、安全登入和免費 Visual Studio 的資訊。"
	services="sql-database"
	keywords="c# 資料庫查詢, c# 查詢, 連接到資料庫, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/17/2016"
	ms.author="annemill"/>


# 使用 Visual Studio 連接到 SQL Database

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

了解如何在 Visual Studio 中連接到 Azure SQL Database。

## 必要條件


若要使用 Visual Studio 來連接到 SQL Database，您必須具有：


- Azure 帳戶和訂用帳戶。您可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。


- Azure SQL Database 服務上的 **AdventureWorksLT** 示範資料庫。
 - 在數分鐘內[建立示範資料庫](sql-database-get-started.md)。


- Visual Studio 2013 Update 4 (或更新版本)。Microsoft 現在*免費*提供 Visual Studio Community。
 - [Visual Studio Community，下載](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [免費 Visual Studio 的更多選項](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - 或是讓本主題中稍後的[步驟](#InstallVSForFree)說明 [Azure 入口網站](https://portal.azure.com/)如何引導您安裝 Visual Studio。


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## 步驟 1：免費安裝 Visual Studio Community


如果您需要安裝 Visual Studio，您可以：

- 將瀏覽器瀏覽至提供免費下載及其他選項的 Visual Studio 網頁以免費安裝 Visual Studio Community。或
- 讓 [Azure 入口網站](https://portal.azure.com/)引導您下載網頁，將於稍後描述。


### 透過 Azure 入口網站的 Visual Studio


1. 透過 [Azure 入口網站](https://portal.azure.com/)登入，位於 http://portal.azure.com/。

2. 按一下 *[全部瀏覽]** > **[SQL Database]**。刀鋒視窗會開啟該資料庫的搜尋。

3. 在靠近頂端的篩選文字方塊中，開始輸入 **AdventureWorksLT** 資料庫的名稱。

4. 當您看到伺服器上資料庫的資料列時，按一下資料列。刀鋒視窗會為您的資料庫開啟。

5. 為了方便起見，請按一下前述每個刀鋒視窗上的最小化控制項。

6. 按一下資料庫刀鋒視窗頂端附近的 [在 Visual Studio 中開啟] 按鈕。關於 Visual Studio 的新刀鋒視窗會利用連結開啟以安裝 Visual Studio 的位置。

	![[在 Visual Studio 中開啟] 按鈕][20-OpenInVisualStudioButton]

7. 按一下 [社群 (免費)] 連結或類似的連結。新的網頁會隨即新增。

8. 使用新網頁上的連結安裝 Visual Studio。

9. 安裝 Visual Studio 之後，於**在 Visual Studio 中開啟**刀鋒視窗上按一下 [在 Visual Studio 中開啟] 按鈕。Visual Studio 隨即開啟。

10. Visual Studio 會提示您填寫對話方塊中的連接字串欄位。
 - 選擇 [SQL Database 驗證]，而非 [Windows 驗證]。
 - 請記得要指定您的 **AdventureWorksLT** 資料庫 (對話方塊中的 [選項] > [連接屬性])。

11. 在 [SQL Server 物件總管] 中，展開資料庫的節點。


## 步驟 2：執行範例查詢

連接到邏輯伺服器後，即可連接到資料庫並執行範例查詢。

1. 在 [物件總管] 中，瀏覽至伺服器上您有權限的資料庫，例如 **AdventureWorks** 範例資料庫。
2. 在資料庫上按一下滑鼠右鍵，然後選取 [新增查詢]。

	![新增查詢。連接到 SQL Database 伺服器：SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. 在查詢視窗中，複製並貼上下列程式碼。

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. 按一下 [執行] 按鈕。下列螢幕擷取畫面顯示成功的查詢。

	![成功。連接到 SQL Database 伺服器：Visual Studio](./media/sql-database-connect-query-ssms/5-success.png)

## 後續步驟

[使用 .NET (C#) 連接到 SQL Database](sql-database-develop-dotnet-simple.md)


<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

<!---HONumber=AcomDC_0817_2016-->