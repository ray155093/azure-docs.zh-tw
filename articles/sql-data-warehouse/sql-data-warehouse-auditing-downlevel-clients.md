<properties
   pageTitle="SQL 資料倉儲下層用戶端對資料稽核的支援 | Microsoft Azure"
   description="了解 SQL 資料倉儲下層用戶端對資料稽核的支援"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/29/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# SQL 資料倉儲 - 下層用戶端對稽核和動態資料遮罩的支援

> [AZURE.SELECTOR]
- [安全性概觀](sql-data-warehouse-overview-manage-security.md)
- [威脅偵測](sql-data-warehouse-security-threat-detection.md)
- [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
- [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [稽核概觀](sql-data-warehouse-auditing-overview.md)
- [稽核下層用戶端](sql-data-warehouse-auditing-downlevel-clients.md)


[稽核](sql-data-warehouse-auditing-overview.md)可與支援 TDS 重新導向的 SQL 用戶端搭配使用。

實作 TDS 7.4 的任何用戶端應該也支援重新導向。例外包括其中未完全支援重新導向功能的 JDBC 4.0，和其中未實作重新導向的 Tedious for Node.JS。

對於「舊版用戶端」，也就是支援 TDS 7.3 版和以下版本 - 應該修改連接字串中的伺服器 FQDN：

連接字串中的原始伺服器 FQDN：<*伺服器名稱*>.database.windows.net

已修改的連接字串中的伺服器 FQDN：<*伺服器名稱*>.database.**secure**.windows.net

「舊版用戶端」的部分清單包括：

- .NET 4.0 和以下版本，
- ODBC 10.0 和以下版本。
- JDBC (雖然 JDBC 支援 TDS 7.4，但並未完整支援 TDS 重新導向功能)
- Tedious (適用於 Node.JS)

**備註：**上述伺服器 FDQN 修改可能會對於套用 SQL Server 層級稽核原則有所助益，不需要每個資料庫中的組態步驟 (暫存緩和)。

<!----HONumber=AcomDC_0907_2016-->