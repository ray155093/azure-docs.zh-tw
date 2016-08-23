<properties
   pageTitle="為 Azure TSQL 上的 SQL Server Stretch Database 啟用透明資料加密 (TDE) | Microsoft Azure"
   description="為 Azure TSQL 上的 SQL Server Stretch Database 啟用透明資料加密 (TDE)"
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
   ms.author="douglaslMS"/>

# 為 Azure 上的 Stretch Database 啟用透明資料加密 (TDE) (Transact-SQL)
> [AZURE.SELECTOR]
- [Azure 入口網站](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

透明資料加密 (TDE) 可在不需變更應用程式的情況下，對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動的威脅。

TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。資料庫加密金鑰是由內建伺服器憑證保護。內建伺服器憑證對每個 Azure 伺服器都是唯一的。Microsoft 至少每 90 天會自動替換這些憑證。如需 TDE 的一般描述，請參閱[透明資料加密 (TDE)]。

##啟用加密

若要為 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 啟用 TDE，請執行下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到裝載資料庫之 Azure 伺服器上的 *master* 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##停用加密

若要為 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 停用 TDE，請執行下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到 *master* 資料庫
2. 執行下列陳述式來加密資料庫。

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##確認加密

若要確認 Azure 資料庫 (儲存從已啟用 Stretch 之 SQL Server 料庫移轉的資料) 的加密狀態，請執行下列步驟：

1. 使用在 master 資料庫中做為系統管理員或 **dbmanager** 角色成員的登入，連接到 *master* 或執行個體資料庫
2. 執行下列陳述式來加密資料庫。

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

```1``` 的結果表示加密的資料庫，```0``` 表示未加密的資料庫。


<!--Anchors-->
[透明資料加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0810_2016------>