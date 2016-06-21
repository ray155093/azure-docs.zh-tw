<properties
   pageTitle="使用 Visual Studio 連接到 SQL 資料倉儲 | Microsoft Azure"
   description="開始連線到 SQL 資料倉儲並執行一些查詢。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/09/2016"
   ms.author="sonyama;barbkess"/>

# 使用 Visual Studio 連接到 SQL 資料倉儲

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)

本逐步解說示範如何使用 Visual Studio 的 SQL Server Data Tools (SSDT) 擴充功能在短時間內連接到 Azure SQL 資料倉儲。一旦連線，您將執行簡單的查詢。

## 必要條件

+ SQL 資料倉儲中的 AdventureWorksDW 範例資料。若要建立此資料庫，請參閱[建立 SQL 資料倉儲][]。
+ Visual Studio 的 SQL Server Data Tools。如需安裝指示和選項，請參閱[安裝 Visual Studio 和 SSDT][]。

## 步驟 1：尋找完整的 Azure SQL 伺服器名稱

您的 SQL 資料倉儲會與 Azure SQL 伺服器相關聯。若要連接至您的資料庫，您需要伺服器的完整名稱 (**servername**.database.windows.net*)。

若要尋找完整的伺服器名稱。

1. 移至 [Azure 入口網站][]。
2. 按一下 [SQL 資料庫]，再按一下您想要連接的資料庫。此範例使用 AdventureWorksDW 範例資料庫。
3. 找出完整的伺服器名稱。

    ![完整伺服器名稱][1]

## 步驟 2：連接到您的 SQL 資料倉儲

1. 開啟 Visual Studio 2013 或 2015。
2. 開啟 [SQL Server 物件總管]。若要這麼做，請選取 [檢視] > [SQL Server 物件總管]。

    ![SQL Server 物件總管][2]

3. 按一下 [加入 SQL Server] 圖示。

    ![加入 SQL Server][3]

4. 填寫 [連線到伺服器] 視窗中的欄位。

    ![連線到伺服器][4]

    - **伺服器名稱**。輸入先前找到的**伺服器名稱**。
    - **驗證**。選取 [SQL Server 驗證] 或 [Active Directory 整合式驗證]。
    - [使用者名稱] 和 [密碼]。如果上面已選取 [SQL Server 驗證]，請輸入使用者名稱和密碼。
    - 按一下 [連接]。

5. 若要瀏覽，請展開您的 Azure SQL 伺服器。您可以檢視與伺服器相關聯的資料庫。展開 AdventureWorksDW 以查看範例資料庫中的資料表。

    ![探索 AdventureWorksDW][5]

## 步驟 3：執行範例查詢

現已建立對您的資料庫的連線，接著繼續撰寫查詢。

1. 在 [SQL Server 物件總管] 中您的資料庫上按一下滑鼠右鍵。

2. 選取 [新增查詢]。新的查詢視窗隨即開啟。

    ![新增查詢][6]

3. 將此 TSQL 查詢複製到查詢視窗中：

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. 執行查詢。若要這麼做，請按一下綠色箭頭，或使用下列快速鍵：`CTRL`+`SHIFT`+`E`。

    ![執行查詢][7]

5. 查看查詢結果。在此範例中，FactInternetSales 資料表有 60398 個資料列。

    ![查詢結果][8]

## 後續步驟

您現在可以連接並查詢，請嘗試[使用 PowerBI 將資料視覺化][]。

若要針對 Windows 驗證設定您的環境，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲][]。

<!--Arcticles-->
[建立 SQL 資料倉儲]: sql-data-warehouse-get-started-provision.md
[安裝 Visual Studio 和 SSDT]: sql-data-warehouse-install-visual-studio.md
[使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 資料倉儲]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[使用 PowerBI 將資料視覺化]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md

<!--Other-->
[Azure 入口網站]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0615_2016-->