<properties
   pageTitle="安裝適用於 SQL 資料倉儲的 Visual Studio 和 SSDT | Microsoft Azure"
   description="安裝適用於 Azure SQL 資料倉儲的 Visual Studio 和 SQL Server Development Tools (SSDT)"
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
   ms.date="07/23/2016"
   ms.author="sonyama;barbkess"/>

# 安裝適用於 SQL 資料倉儲的 Visual Studio 2015 和 SSDT

若要開發 SQL 資料倉儲的應用程式，建議使用 Visual Studio 2015 並搭配最新版本的 SQL Server Data Tools (SSDT)。Visual Studio 2013 Update 5 搭配 SSDT 也支援回溯相容性。

搭配使用 Visual Studio 和 SSDT 可讓您使用 SQL Server 物件總管，經由視覺化方式瀏覽資料表、檢視、預存程序和 SQL 資料倉儲中的其他許多物件，以及執行查詢。

> [AZURE.NOTE] SQL 資料倉儲尚未支援 Visual Studio 資料庫專案。此功能將會加入未來的版本中。

## 步驟 1：安裝 Visual Studio 2015

遵循下列連結來下載並安裝 Visual Studio 2015。如果已安裝 Visual Studio 2013 或 2015，可以跳至步驟 2 安裝 SSDT。

1. [下載 Visual Studio 2015][]。
2. 依照 MSDN 上的[安裝 Visual Studio][] 指南並選擇預設組態。

## 步驟 2：安裝 SSDT

若要安裝適用於 Visual Studio 的 SSDT，只需依照下列步驟，從 Visual Studio 檢查 SSDT 更新。

1. 在 Visual Studio 中，按一下 [工具] / [擴充功能和更新] / [更新]
2. 選取 [產品更新]，然後尋找 [資料庫工具適用的 Microsoft SQL Server 更新]

如果找不到更新，表示您應該已安裝最新版本。若要確認已安裝 SSDT，請按一下 [說明] / [關於 Microsoft Visual Studio] 並在清單中尋找 SQL Server Data Tools。SSDT 的最新版本是 14.0.60525.0。如果無法在 Visual Studio 中使用安裝選項，或者您也可以瀏覽 [SSDT 下載][]頁面，手動下載和安裝 SSDT。

## 後續步驟

既然有了最新版本的 SSDT，您可以開始[連接][]到 SQL 資料倉儲。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[連接]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[下載 Visual Studio 2015]: https://www.visualstudio.com/downloads/
[安裝 Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT 下載]: https://msdn.microsoft.com/library/mt204009.aspx

<!---HONumber=AcomDC_0727_2016-->