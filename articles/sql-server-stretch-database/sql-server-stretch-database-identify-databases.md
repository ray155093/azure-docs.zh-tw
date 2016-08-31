<properties
	pageTitle="透過執行 Stretch Database Advisor 為資料庫和資料表識別 Stretch Database | Microsoft Azure"
	description="了解如何識別可做為 Stretch Database 候選對象的資料庫和資料表。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# 透過執行 Stretch Database Advisor 為資料庫和資料表識別 Stretch Database

若要識別可做為 Stretch Database 候選對象的資料庫和資料表，請下載 SQL Server 2016 Upgrade Advisor，並執行 Stretch Database Advisor。Stretch Database Advisor 也能找出阻礙問題。

## 下載並安裝 Upgrade Advisor
從[這裡](http://go.microsoft.com/fwlink/?LinkID=613421)下載並安裝 Upgrade Advisor。此工具並不包含 SQL Server 安裝媒體。

## 執行 Stretch Database Advisor

1.  執行 Upgrade Advisor。

2.  選取 [案例]，然後選取 [執行 STRETCH DATABASE ADVISOR]。

3.  在 [執行 Stretch Database Advisor] 刀鋒視窗上，按一下 [選取要分析的資料庫]。

4.  在 [選取資料庫] 刀鋒視窗上，輸入或選取伺服器名稱和驗證資訊。按一下 [連接]。

5.  隨即出現所選取伺服器上的資料庫清單。選取您想要分析的資料庫。按一下 [選取]。

6.  在 [執行 Stretch Database Advisor] 刀鋒視窗上，按一下 [執行]。分析將會執行。

## 檢閱結果

1.  當分析完成時，在 [分析資料庫] 刀鋒視窗上，選取您已分析的其中一個資料庫以顯示 [分析結果] 刀鋒視窗。

    [分析結果] 刀鋒視窗會列出所選取資料庫中符合預設建議準則的建議資料表。

2.  在 [分析結果] 刀鋒視窗上的資料表清單中，選取其中一個建議資料表以顯示 [資料表結果] 刀鋒視窗。

    如果發生封鎖問題，[資料表結果] 刀鋒視窗就會列出所選取資料表的封鎖問題。如需 Stretch Database Advisor 所偵測到的封鎖問題相關資訊，請參閱 [Stretch Database 的限制](sql-server-stretch-database-limitations.md)。

3.  在 [資料表結果] 刀鋒視窗中的封鎖問題清單中，選取其中一個問題以顯示所選取問題的詳細資訊，以及建議的因應步驟。如果您想要為選取的資料表設定 Stretch Database，請實作建議的風險降低步驟。

## 後續步驟
啟用 Stretch Database。

-   若要在**資料庫**上啟用 Stretch Database，請參閱[為資料庫啟用 Stretch Database](sql-server-stretch-database-enable-database.md)。

-   若要在已於該資料庫上啟用 Stretch 的情況下，於另一個**資料表**上啟用 Stretch Database，請參閱[為資料表啟用 Stretch Database](sql-server-stretch-database-enable-table.md)。

## 另請參閱

[Stretch Database 的限制](sql-server-stretch-database-limitations.md)

[為資料庫啟用 Stretch Database](sql-server-stretch-database-enable-database.md)

[為資料表啟用 Stretch Database](sql-server-stretch-database-enable-table.md)

[所有 Azure SQL Server Stretch Database 服務的主題](sql-server-stretch-database-index-all-articles.md)

<!---HONumber=AcomDC_0817_2016-->