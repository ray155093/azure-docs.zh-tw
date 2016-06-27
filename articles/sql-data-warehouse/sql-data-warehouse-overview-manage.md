<properties
   pageTitle="管理 Azure SQL 資料倉儲中的資料庫 | Microsoft Azure"
   description="管理 SQL 資料倉儲資料庫的概觀。包含管理工具、DWU 與相應放大效能、疑難排解查詢效能、建立良好的安全性原則，以及從資料損毀或區域性停電還原資料庫。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama;"/>

# 管理 Azure SQL 資料倉儲中的資料庫

SQL 資料倉儲會自動化管理您的資料庫的各個層面。例如，若要調整效能，您只需要調整並支付適當等級的計算資源，然後讓 SQL 資料倉儲執行相應放大和調整回來的所有工作。

您一定想要監視工作負載來識別您的效能需求，以及對長時間執行的查詢進行疑難排解。您也必須執行幾項安全性工作，管理使用者與登入的權限。

本概觀涵蓋管理 SQL 資料倉儲的這些層面。

- 管理工具
- 調整計算
- 暫停與繼續
- 效能最佳作法
- 查詢監控
- Security
- 備份與還原

## 管理工具

您可以使用各種工具來管理 SQL 資料倉儲中的資料庫。管理資料庫時，您將針對您需要執行的各種類型工作開發工具喜好設定。

### Azure 入口網站
[Azure 入口網站][]是網頁型入口網站，您可以在當中建立、更新和刪除資料庫以及監視資料庫資源。如果您剛開始使用 Azure、管理少量的資料倉儲資料庫，或是需要快速執行工作，那這正是十分適合您的工具。

若要開始使用 Azure 入口網站，請參閱[建立 SQL 資料倉儲 (Azure 入口網站)][]。

### Visual Studio 中的 SQL Server Data Tools
Visual Studio 中的 [SQL Server Data Tools][] (SSDT)，可讓您連接、管理和開發資料庫。如果您是熟悉 Visual Studio 或其他整合式開發環境 (IDE) 的應用程式開發人員，請嘗試使用 Visual Studio 中的 SSDT。

SSDT 包含的 SQL Server 物件總管，可讓您針對 SQL 資料倉儲資料庫視覺化、連接和執行指令碼。若要快速連線至 SQL 資料倉儲，只需在 Azure 傳統入口網站檢視資料庫的詳細資料時，按一下命令列中的 [在 Visual Studio 中開啟] 按鈕即可。

若要在 Visual Studio 中開始使用 SSDT，請參閱[使用 Visual Studio 連接到 Azure SQL 資料倉儲][]。

### 命令列工具
命令列工具非常適合自動化您的工作負載。PowerShell 和 sqlcmd 是自動化程序的兩個好方法。因為您可為必要的工作編寫指令碼並自動執行這類工作，所以我們建議使用這些工具來管理大量的邏輯伺服器，以及在生產環境中部署資源變更。

### 動態管理檢視 

DMV 是管理 SQL 資料倉儲的要素。入口網站上幾乎所有的資訊都依賴 DMV。若要查看 SQL 資料倉儲 DMV 的清單，請參閱 [SQL 資料倉儲系統檢視][]。

若要開始，請參閱[使用 sqlcmd 連接和查詢][]和[建立資料庫 (PowerShell)][]。

## 調整計算

在 SQL 資料倉儲中，您可以快速地將效能相應放大或調整回來，方法是增加或減少 CPU、記憶體和 I/O 頻寬的計算資源。若要調整效能，您只需要調整 SQL 資料倉儲配置到您的資料庫的資料倉儲單位 (DWU) 的數目。SQL 資料倉儲會快速進行變更，並處理硬體或軟體的所有基礎變更。

若要深入了解如何調整 DWU，請參閱[調整效能][]。

##  暫停與繼續

為了節省成本，您可以隨選暫停和繼續計算資源。例如，如果您在夜間和週末不會使用資料庫，可以在這段時間暫停，並且在白天時繼續。資料庫暫停時，不會向您收取 DWU 的費用。

如需詳細資訊，請參閱[暫停計算][]和[繼續計算][]。

## 效能最佳作法

開始使用新技術時，探索一開始就最能節省您的時間的最佳秘訣和竅門。您可以在許多主題中找到我們的最佳作法。

若要查看在開發您的工作負載時最重要考量的摘要，請參閱 [SQL 資料倉儲最佳作法][]。

## 查詢監控

有時候查詢執行時間太久，但您不確定哪一個是罪魁禍首。SQL 資料倉儲具有動態管理檢視 (DMV)，可讓您找出哪一個查詢的時間太長。

若要找出長時間執行的查詢，請參閱[使用 DMV 監視工作負載][]。

## Security

若要維護安全系統，您必須提高警覺，並且防範任何類型的未經授權存取。安全性系統必須確定防火牆規則作用中，只有獲得授權的 IP 位址可以連接。它需要使用者認證的適當驗證。使用者已連接到資料庫之後，使用者應該只有執行最少動作的權限。若要保護資料，您可以使用加密。稽核和追蹤也同樣重要，這樣您就可以在有任何可疑的活動時追溯事件。

若要了解如何管理安全性，請前往[安全性概觀][]。

## 備份與還原

對生產環境資料庫來說，擁有可靠的資料備份是不可或缺的一部分。「SQL 資料倉儲」會自動定期備份您的作用中資料庫來維護您的資料安全。這些備份可讓您從已損毀資料或不小心卸除資料或資料庫的情況下復原。若要了解資料備份排程、保留原則及如何還原資料庫，請參閱[從快照還原][]。

## 後續步驟
使用良好的資料庫設計原則，會讓在 SQL 資料倉儲中管理您的資料庫更容易。若要深入了解，請前往[開發概觀][]。

<!--Image references-->

<!--Article references-->
[建立 SQL 資料倉儲 (Azure 入口網站)]: sql-data-warehouse-get-started-provision.md
[建立資料庫 (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[使用 Visual Studio 連接到 Azure SQL 資料倉儲]: sql-data-warehouse-get-started-connect.md
[使用 sqlcmd 連接和查詢]: sql-data-warehouse-get-started-connect-sqlcmd.md
[開發概觀]: sql-data-warehouse-overview-development.md
[使用 DMV 監視工作負載]: sql-data-warehouse-manage-monitor.md
[暫停計算]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[從快照還原]: sql-data-warehouse-restore-database-overview.md
[繼續計算]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[調整效能]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[安全性概觀]: sql-data-warehouse-overview-manage-security.md
[SQL 資料倉儲最佳作法]: sql-data-warehouse-best-practices.md
[SQL 資料倉儲系統檢視]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure 入口網站]: http://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->