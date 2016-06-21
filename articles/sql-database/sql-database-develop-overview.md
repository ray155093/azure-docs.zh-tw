<properties
	pageTitle="SQL Database 開發概觀 | Microsoft Azure"
	description="深入了解連接至 SQL Database 的應用程式的可用連線庫和最佳作法。"
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="annemill"/>

# SQL Database 開發概觀
本文將逐步解說開發人員在撰寫程式碼以連接到 Azure SQL Database 時應考慮的的基本考量事項。

## 語言和平台
各種程式設計語言和平台有程式碼範例可用：您可以在下列位置找到程式碼範例的連結：

* 詳細資訊：[SQL Database 和 SQL Server 的連線庫](sql-database-libraries.md)

## 資源限制
Azure SQL Database 使用兩種不同機制來管理資料庫可使用的資源：資源管理和限制強制執行。

* 詳細資訊：[Azure SQL Database 資源限制](sql-database-resource-limits.md)

## 安全性
Azure SQL Database 提供資源以在 SQL Database 上限制存取、保護資料，以及監視活動。

* 詳細資訊：[保護您的 SQL Database](sql-database-security.md)

## 驗證
* Windows 驗證無法在 Azure SQL Database 中使用，您必須改為使用 SQL Database 驗證。
* 您必須指定一個特定的資料庫，而非預設為 *master* 資料庫。
* 您無法在 SQL Database 上使用 **USE myDatabaseName;** 陳述式來切換到其他資料庫。
* 詳細資訊：[SQL Database 安全性：管理資料庫存取與登入安全性](sql-database-manage-logins.md)

## 復原功能
當連接到 SQL Database 發生暫時性錯誤時，您的程式碼應該重試呼叫。我們建議讓重試邏輯使用輪詢邏輯，如此就不會因多個用戶端同時重試而對 SQL Database 造成不必要的負荷。

* 程式碼範例︰如需示範重試邏輯的程式碼範例，請在以下位置參閱您選擇的語言的範例︰[SQL Database 和 SQL Server 的連線庫](sql-database-libraries.md)
* 詳細資訊：[SQL Database 用戶端程式的錯誤訊息](sql-database-develop-error-messages.md)

## 管理連接
* 在您的用戶端連線邏輯中，將預設的逾時覆寫為 30 秒。預設 15 秒對於依賴網際網路的連線而言太短。
* 如果您使用的是[連接集區](http://msdn.microsoft.com/library/8xx3tyca.aspx)，請確定在程式未主動使用時即時關閉連接，而不是準備重複使用連接。

## 網路考量事項
* 在託管您的用戶端程式的電腦上，請確定防火牆允許連接埠 1433 上的傳出 TCP 通訊。詳細資訊：[設定 Azure SQL Database 防火牆](sql-database-configure-firewall-settings.md)
* 如果您的用戶端是在 Azure 虛擬機器 (VM) 上執行，而用戶端程式會連接到 SQL Database V12，您就必須開啟該 VM 上特定的連接埠範圍。如需詳細資訊，請參閱：[針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)
* 與 Azure SQL Database V12 的用戶端連線有時會略過 proxy 並直接與資料庫互動。1433 以外的連接埠變得重要。詳細資訊：[針對 ADO.NET 4.5 和 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)

## 使用 Elastic Scale 的資料分區化
Elastic Scale 會簡化相應放大 (和相應縮小) 的程序。

* [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)
* [開始使用 Azure SQL Database Elastic Scale 預覽](sql-database-elastic-scale-get-started.md)

<!---HONumber=AcomDC_0608_2016-->