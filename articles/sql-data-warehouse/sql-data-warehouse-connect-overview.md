<properties
   pageTitle="連線到 Azure SQL 資料倉儲 | Microsoft Azure"
   description="連接到 Azure SQL 資料倉儲的連線概觀"
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
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# 連接到 Azure SQL 資料倉儲

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-connect-overview.md)
- [驗證](sql-data-warehouse-authentication.md)
- [驅動程式](sql-data-warehouse-connection-strings.md)

連接到 Azure SQL 資料倉儲的概觀。

## 從入口網站探索連接字串

您的 SQL 資料倉儲會與 Azure SQL 伺服器相關聯。若要進行連接，您需要伺服器的完整名稱 。例如，**myserver**.database.windows.net。

若要尋找完整的伺服器名稱：

1. 移至 [Azure 入口網站][]。
2. 按一下 [SQL 資料庫]，再按一下您想要連接的資料庫。此範例使用 AdventureWorksDW 範例資料庫。
3. 找出完整的伺服器名稱。

    ![完整伺服器名稱][1]

## 連線設定

SQL 資料倉儲會在連線和建立物件期間標準化一些設定。這些不會被覆寫。

| 資料庫設定 | 值 |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | 開啟 |
| [QUOTED\_IDENTIFIERS][] | 開啟 |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## 監視連線和查詢

建立連線和工作階段之後，您就可以撰寫查詢並將其提交至 SQL 資料倉儲。若要了解如何監視工作階段和查詢，請參閱[使用 DMV 監視工作負載][]。

## 後續步驟

若要透過 Visual Studio 和其他應用程式開始查詢您的資料倉儲，請參閱[使用 Visual Studio 查詢][]。

<!--Articles-->
[使用 Visual Studio 查詢]: ./sql-data-warehouse-query-visual-studio.md
[使用 DMV 監視工作負載]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure 入口網站]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0831_2016-->